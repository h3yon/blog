---
title: "SpringFramework Naver Login 구현하기"
date: 2021-08-14T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
  - oauth
---


## 스프링부트 Naver Login 구현하기

출처:

- 이동욱 님의 스프링 부트와 AWS로 혼자 구현하는 웹 서비스
- [스프링 부트와 OAuth2 Naver](https://velog.io/@guswns3371/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8%EC%99%80-OAuth2-Naver)

## 사용한 방식

저번에 카카오 로그인을 구현하였을 때는 restTemplate을 사용했었다.
그런데 이번에는 `spring-security-oauth2` 부분을 알아보고 사용해보고자 했다.

라이브러리 중에 `spring-security-auth2-autoconfigure`이 있는데,
스프링부트2에서 기존 설정을 그대로 사용할 수 있어 많은 개발자가 이 방식을 사용했다고 한다.

하지만 책에서는 `spring-boot-starter-oauth2-client` 라이브러리를 사용했다.
그 이유는 아래와 같다.

- 스프링 팀에서 신규 기능은 oauth2 라이브러리에서만 지원하겠다고 선언
- 스프링부트용 라이브러리가 출시
- 기존에 사용되던 방식은 확장 포인트가 적절하게 open되어 있지 않아 직접 상속하거나,
  오버라이딩 해야하고, 신규 라이브러리의 경우 확장 포인트를 고려해서 설계된 상태

직접 구현할 때 관련 자료를 찾아보면,

1. application.properties 혹은 application.yml
2. spring-security-oauth2-autoconfigure 라이브러리를 사용했는지
   에 대한 의문을 가질 수 있다.

## 의문점 해결하기

1. application.properties 혹은 application.yml

   <h5>1) application.properties </h5>
   application.properties의 구조는 아래와 같다.
   spring 안에 datasource가 있을 경우 '.'을 사용하여 계층적 데이터를 표시함을 알 수 있다.
   또, 아래처럼 '#---'을 사용하여 여러 문서로 분할할 수 있다.

   ```
   #---
   spring.config.activate.on-profile=dev
   spring.datasource.password=password
   #---
   spring.config.activate.on-profile=prod
   spring.datasource.password=password
   ```

   <h5>2) application.yml </h5>

   application.yml에서는 계층 구조가 아래처럼 나타남을 알 수 있고,
   '---'을 사용하여 여러 문서 파일을 지원함을 알 수 있다.

   ```
   spring:
      datasource:
         password: password
         url: jdbc:h2:dev
         username: SA
   ---
   spring:
         config:
            activate:
               on-profile: staging
         datasource:
            password: 'password'
            url: jdbc:h2:staging
            username: SA
   ```

   - application.yml의 이점

     둘이 어떤 케이스에 각각 사용되는지가 제일 궁금해서 검색을 해보았는데,
     그 내용은 아래와 같다.

     - application.yml 파일이 각종 설정값을 관리하는데 더 편하고,
       YAML은 JSON의 확장판과 같기 때문에 가독성 측면에서 좋다
     - properties 파일을 많이 사용하였지만 표현의 한계로 yaml 파일을 더 많이 사용하게 되었다.

     위의 이유로 yml 파일을 주로 사용함을 알 수 있다.

## 실습 시작!

우선 build.gradle로 가서 의존성 하나를 추가해준다.

```
# build.gradle
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'
```

해당 의존성은 소셜 로그인 등 클라이언트 입장에서 소셜 기능 구현 시 필요한 의존성
그럼 소셜 로그인 코드를 작성하기 앞서,

`config/auth`패키지를 생성해준다.

## SecurityConfig

```
@RequiredArgsConstructor
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    private final CustomOAuth2UserService customOAuth2UserService;

    @Override
    protected void configure(HttpSecurity http) throws Exception{
        http
                .csrf().disable().headers().frameOptions().disable().and()
                .authorizeRequests()
                .andMatchers("/","/css/**","/images/**","/js/**","/h2-console/**", "/profile").permitAll()
                .andMatchers("/api/v1/**").hasRole(Role.USER.name())
                .anyRequest().authenticated()
                .and()
                .logout()
                .logoutSuccessUrl("/")
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .userService(customOAuth2UserService);
    }
}
```

- @EnableWebSecurity:
  Spring Security 설정들을 활성화시켜줍니다.
- .csrf().disable().headers().frameOptions().disable().and():
  h2-console 화면을 사용하기 위해 해당 옵션들을 disable하는 부분
- .authorizeRequests():
  URL 별 권한 관리를 설정하는 옵션의 시작점
  이 부분을 넣어주어야 andMatchers() 옵션 사용 가능
- .andMatchers("/","/css/\*\*", ...).permitAll():
  matchers 안에 있는 "/" 같은 부분들은 permitAll() 옵션으로 전체 열람 권한 줄 수 O
- anyRequest().authenticated():
  설정된 값 이외 나머지 URL은 인증된 사용자들에게만 허용
- .logout().logoutSuccessUrl("/")
  로그아웃 기능에 대한 여러 설정의 진입점
- .oauth2Login()
  oauth2 로그인 기능에 대한 여러 설정 진입점
- .userInfoEndPoint()
  oAuth2 로그인 성공 이후 사용자 정보를 가져올 때의 설정 담당
- .userService
  소셜 로그인 성공 시 후속 조치 진행. 추가로 진행하고자 하는 기능 명시 가능
  > OAuth2UserService 인터페이스의 추상메서드인 loadUser를 사용

## resources/application-oauth.yml

resources에 application.yml 파일이 있을텐데,
resources/application-oauth.yml 파일 또한 만들어준다.

```
# resources/application-oauth.yml

spring:
  security:
    oauth2:
      client:
        registration:
          naver:
            client-id: l4Z5n7Dr8puo1xal22dq
            client-secret: fpoi6aZ8SO
            redirect-uri: "{baseUrl}/{action}/oauth2/code/{registrationId}"
            authorization-grant-type: authorization_code
            scope: name,email,profile_image
            client-name: Naver
        provider:
          naver:
            authorization-uri: https://nid.naver.com/oauth2.0/authorize
            token-uri: https://nid.naver.com/oauth2.0/token
            user-info-uri: https://openapi.naver.com/v1/nid/me
            user-name-attribute: response
```

네이버는 Spring Security를 공식 지원하지 않기 때문에,
위에처럼 provider 값들을 수동으로 입력한다.
그래서 예를 들어 http://localhost:8080/oauth2/authorization/naver URL 값을 넣어주면

그럼 해당 oauth.yml 파일을 사용할 것이기 때문에
아래 profiles 부분을 spring 아래 아무데에나 넣어준다.

```
# resources/application.yml

spring:
...
profiles:
include: oauth
...

```

## user/entity

아래처럼 userEntity가 있다고 가정한다.

```java
/**
 * user/entity/User.java
 */

@Getter
@NoArgsConstructor
@Entity
public class User extends BaseTimeEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String email;

    @Column
    private String picture;

   // Enumerated: Enum 값을 어떤 형태로 저장할지에 대한 설정(Default = INT)
    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private Role role;

    @Builder
    public User(String name, String email, String picture, Role role){
        this.name = name;
        this.email = email;
        this.picture = picture;
        this.role = role;
    }

    public User update(String name, String picture){
        this.name = name;
        this.picture = picture;

        return this;
    }

    public String getRoleKey(){
        return this.role.getKey();
    }
}
```

위에서 Role을 넣어주었기 때문에
Role.java 파일을 만들어준다.

```java
/**
 * user/entity/Role을.java
 */
@Getter
@RequiredArgsConstructor
public enum Role {
    GUEST("ROLE_GUEST", "손님"),
    USER("ROLE_USER", "일반 사용자");

    private final String key;
    private final String title;
}
```

스프링 security에서는 권한 코드에 항상 'ROLE\_'이 앞에 있어야 한다.

## user/repository/UserRepository

```java
/**
 * user/repository/UserRepository.java
 */
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

원하는 걸 사용할 수 있는데,
책의 예제에서는 findByEmail을 하였음을 알 수 있다.

## config/auth/CustomOAuth2UserService

```java
/**
 * config/auth/CustomOAuth2UserService.java
 */

@RequiredArgsConstructor
@Service
public class CustomOAuth2UserService implements OAuth2UserService<OAuth2UserRequest, OAuth2User> {
    private final UserRepository userRepository;
    private final HttpSession httpSession;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {

       /**
        * DefaultOAuth2UserService는 OAuth2UserService의 구현체
        * - 해당 클래스를 이용해서 userRequest에 있는 정보를 빼낼 수 있습니다.
        * - objectMapper를 이용해서 json 형식으로 출력 가능(new ObjectMapper().writerWithDefaultPrettyPrinter().writeValueAsString(userRequest))
        */
        OAuth2UserService delegate = new DefaultOAuth2UserService();
        OAuth2User oAuth2User = delegate.loadUser(userRequest);

        /**
         * 변수에 대한 설명
         * registrationId: 로그인 진행중인 서비스를 구분하는 코드 (구글, 네이버)
         * userNameAttributeName: OAuth2 로그인 진행시 키가 되는 필드값 (PK 같은 역할) - 구글은 제공하지만 네이버, 카카오는 제공 X
         * attributes: OAuth2UserService 를 통해 가져온 OAuth2User 클래스의 attribute를 담을 클래스
         * - oAuth2User: OAuth2UserService 로 만들어진 OAuth2User 객체를 참조하는 변수
         * - custom 정의 클래스
         */
        String registrationId = userRequest.getClientRegistration().getRegistrationId();
        String userNameAttributeName = userRequest.getClientRegistration().getProviderDetails()
                .getUserInfoEndpoint().getUserNameAttributeName();
        OAuthAttributes attributes = OAuthAttributes.of(registrationId, userNameAttributeName, oAuth2User.getAttributes());

        User user = saveOrUpdate(attributes);

        // 세션에 자용자 정보를 저장하기 위한 DTO 클래스. User 클래스를 사용하면 안되기에 SessionUser
        // 왜 사용하면 안 될까?? 직렬화 관련 에러 + User 클래스가 데이터베이스와 직접 연결되는 엔티티여서
        // 렬화 기능을 가진 DTO를 하나 추가로 만드는 것이 이후 운영 및 유지보수 때 많은 도움
        httpSession.setAttribute("user", new SessionUser(user));

        return new DefaultOAuth2User(Collections.singleton(new SimpleGrantedAuthority(user.getRoleKey())),
                attributes.getAttributes(),
                attributes.getNameAttributeKey());
    }

    private User saveOrUpdate(OAuthAttributes attributes){
        User user = userRepository.findByEmail(attributes.getEmail())
                .map(entity -> entity.update(attributes.getName(), attributes.getPicture()))
                .orElse(attributes.toEntity());

        return userRepository.save(user);
    }
}
```

- RequiredArgsConstructor: 초기화 되지않은 final 필드나, @NonNull 이 붙은 필드에 대해 생성자를 생성
- 'CustomOAuth2UserService 클래스까지 생성되었다면 OAuthAttributes 클래스를 생성

## config/auth/dto/OAuthAttributes

OAuthAttributes를 봐보자.

```java
@Getter
public class OAuthAttributes {
    private Map<String, Object> attributes;
    private String nameAttributeKey;
    private String name;
    private String email;
    private String picture;

    @Builder
    public OAuthAttributes(Map<String, Object> attributes, String nameAttributeKey, String name, String email, String picture){
        this.attributes = attributes;
        this.nameAttributeKey = nameAttributeKey;
        this.name = name;
        this.email = email;
        this.picture = picture;
    }

    /* OAuth2User에서 반환하는 사용자 정보는 Map 자료구조 형태이기에 값 하나하나를 변환 */
    public static OAuthAttributes of(String registrationId, String userNameAttributeName,
                                     Map<String ,Object> attributes) {
            return ofNaver("id", attributes);
    }

    private static OAuthAttributes ofNaver(String userNameAttributeName, Map<String,Object> attributes) {
        Map<String,Object> response = (Map<String, Object>) attributes.get("response");

        return OAuthAttributes.builder()
                .name((String) response.get("name"))
                .email((String) response.get("email"))
                .picture((String) response.get("profile_image"))
                .attributes(response)
                .nameAttributeKey(userNameAttributeName)
                .build();
    }


    /* 가입 기본 권한: GUEST. User 엔티티 생성 */
    public User toEntity(){
        return User.builder()
                .name(name)
                .email(email)
                .picture(picture)
                .role(Role.GUEST)
                .build();
    }
}
```

## config/auth/dto/SessionUser

```java
@Getter
public class SessionUser implements Serializable {
    private String name;
    private String email;
    private String picture;

    public SessionUser(User user) {
        this.name = user.getName();
        this.email = user.getEmail();
        this.picture = user.getPicture();
    }
}
```

인증된 사용자 정보만 필요합니다.
