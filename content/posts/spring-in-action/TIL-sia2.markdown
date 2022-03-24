---
title: "[스프링 인 액션5] Chapter4~6 내용 정리"
date: 2021-10-19T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring in action
  - spring
---


## Chapter4 스프링 시큐리티


사용 예시로 알아보기


```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


	//HTTP 보안 구성
    @Override
    protected void configure(HttpSecurity http) throws Exception{ 
        http
                .authorizeRequests()										// ExpressionInterceptUrlRegistry 객체 반환
                .antMatchers("/design", "/orders") 					// 이 부분은
                .access("hasRole('ROLE_USER')") 					// 인가 과정
                .antMatchers("/", "/**").access("permitAll") 		// 이 부분은 누구나 접속 가능
                .and()
                .httpBasic();
    }

	//사용자 인증 정보 구성
    @Override
    protected void configure(AuthenticationManager auth) throws Exception{
        auth.inMemoryAuthentication()
				...
}
```

여러가지 사용자 스토어 구성 방법 4가지

- 인메모리 사용자 스토어
- JDBC 기반 사용자 스토어
- LDAP 기반 사용자 스토어
- 커스텀 사용자 명세 서비스


### 4.2.1인메모리 사용자 스토어


메모리 = 사용자 정보를 유지, 관리할 수 있는 곳 중 하나


```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


	...


    @Override
    protected void configure(AuthenticationManager auth) throws Exception{
        auth.inMemoryAuthentication()
                .withUser("user1")
                .password("{noop}password1") 		//{noop} 비밀번호 암호화X
                .authorities("ROLE_USER")
                .and()
                .withUser("user2")
                .password("{noop}password2")
                .authorities("ROLE_USER");
    }
}
```

withUser로 사용자 구성 시작 -> 이름, 비밀번호, 권한을 인자로 전달한다.
ex) withUser("name).password("pw").authorities("ROLE_USER")
+ authorities("ROLE_USER") 대신 .roles("USER")를 사용해도 O
+ password() 메서드 호출하여 암호화 하지 않으면 HTTP 403/500 에러(접근 거부/Internal Server Error)



### 4.2.2 JDBC 기반의 사용자 스토어
사용자 정보는 관계형 데이터베이스로 유지, 관리되는 경우가 많아 JDBC 기반 사용자 스토어가 적합

이 부분도 마찬가지로 PasswordEncoder를 사용해서 비밀번호 암호화 필요


```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


	...


    @Override
    protected void configure(AuthenticationManager auth) throws Exception{
         auth
            .jdbcAuthentication()
            .dataSource(dataSource)
            // 스프링 시큐리티와 다른 데이터베이스(테이블이나 열의 이름이 다를 때)를 사용할 경우
            .usersByUsernameQuery("select username, password, enabled, from users where username=?")
            .authoritiesByUsernameQuery("select username, authority from authorities where username=?")
			.passwordEncoder(new BCryptPasswordEncoder());
	}
}
```

스프링 시큐리티의 내부 코드에서는

```
DEF_USERS_BY_USERNAME_QUERY = "select username ,password, enabled from users where username = ?"
DEF_AUTHORITIES_BY_USERNAME_QUERY = "select username, authority from authorities where username = ?"
DEF_GROUP_AUTHORITIES_BY_USERNAME_QUERY = "select g.id, g.group_name, ga.authority from authorities g, group_members gm, group_authorities ga where gm.username = ? and g.id = ga.group_id and g.id = gm.group_id";
```

를 사용하고 위에 코드처럼 custom SQL 쿼리로 대체할 수 있다.

→ 조건의 경우, where절에 사용되는 매개변수는 하나이며, username이어야 한다. 사용자 정보 인증 쿼리에서 username, password, enabled 열 값을 반환해야 한다.



### 4.2.3 LDAP 기반 사용자 스토어


LDAP(Lightweight Directory Access Protocol) 기반 인증은 `ldapAuthentication()` 메서드를 사용할 수 있다.

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


	...


    @Override
    protected void configure(AuthenticationManager auth) throws Exception{
			auth.ldapAuthentication()
				// .userSearchBase("ou=people") 					<- 이렇게 기준점 쿼리를 지정 가능(구성 단위)
    	   	 	.userSearchFilter("(uid={0})")
        		.groupSearchFilter("member={0}");
	}
}
```

LDAP의 기본 인증 전략은 사용자가 직접 LDAP 서버에서 인증받도록 하는 것.
외에도 비밀번호를 비교하는 방법도 O
입력된 비밀번호를 LDAP 디렉토리에 전송한 후, 비밀번호를 비밀번호 속성 값과 비교하도록 LDAP 서버에 요청한다.(이 비교는 LDAP 서버에서 수행)

→ 비교 시 passwordCompare() 메서드 호출하면 된다.

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


	...


    @Override
    protected void configure(AuthenticationManager auth) throws Exception{
			auth.ldapAuthentication()
    	   	 	.userSearchFilter("(uid={0})")
        		.groupSearchFilter("member={0}");
				.passwordCompare() 													// 비교하고
				.passwordEncoder(new BCryptPasswordEncoder()) 		//암호화하고
				.passwordAttribute("userPassword"); 							// password 대신 userPassword로 지정해놔서 그걸로 진행
	}
}
```

LDAP 서버?


기본적으로 localhost:33389.
contextSource메서드로 서버 위치 구성 가능 → ContextSourceBuilder 반환


```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {


	...


    @Override
    protected void configure(AuthenticationManager auth) throws Exception{
		...
        .passwordCompare()
        .passwordEncoder(new BCryptPasswordEncoder())
        .passwordAttribute("userPassword");
        .contextSource()
        .root("dc=tacocloud,dc=com")			//url도 가능
        .ldif("classpath:users.ldif")        			//LDAP 데이터를 나타내는 표준화된 방법. 자동으로 users.ldif 로드


	}
}
```
로그인이 되게 하려면 미리 정해둔 비밀번호를 암호화한 결과값을 알아낸 후

LDIF 파일 사용자의 passCode 값으로 교체하고 application 다시 시작



### 4.2.4 사용자 인증의 커스터마이징
(이건 코드로 보기)

```java
@Entity
public class User implements UserDetails {
	...
}
```

스프링 시큐리티의 UserDetails를 구현하면

getAuthorities() → 부여된 권한 저장한 컬렉션 반환

외에도 is → AccountNonExpired()/NonLocked()/CredentialsNonExpired()/isEnabled() 존재



+ 내장된 UserDetailsService.

→ User에서는 UserDetails를 구현. UserRepository에서는 findByUsername()을 제공하므로 UserDetailsService 구현 클래스에서 사용해야 하는 모든 것이 준비된 상태

```java
public interface UserDetailsService{
	UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

그럼 UserDetailsService를 구현하는 클래스!



### 4.3.2 커스텀 로그인 페이지

```java
    http
            .authorizeRequests()
            .antMatchers("/design", "/orders")
            .access("hasRole('ROLE_USER')")
            .antMatchers("/", "/**").access("permitAll")
            .and()
            .formLogin()      								//이 부분 추가
            .loginPage("/login");
			.dafaultSuccessUrl("/design", true)		// 로그인 후 이동할 페이지
			.loginProcessingUrl("/authenticate")		//해당 요청으로 로그인 처리	
			.usernameParameter("user") 				//필요할 경우 이렇게 필드 이름도 설정 가능
```

→ WebConfig에도 뷰만 뿌려주면 되기 때문에 /login 페이지 추가



로그아웃하기

```java
    http.
			...
			.and()
			.logout()
			.logoutSuccessUrl("/");	
```

### 4.4 사용자 인지하기


사용자 인지하는 방법 중 많이 사용되는 방법은 아래와 같다

Principal 객체를 컨트롤러 메서드에 주입
Authentication 객체를 컨트롤러 메서드에 주입
SecurityContextHolder를 사용해서 보안 컨텍스트를 얻기
@AuthenticationPrincipal 애노테이션을 메서드에 저장


@AuthenticationPrincipal로 사용자 인지

```java
@PostMapping
public String processOrder(@Valid Order order, ...
        , @AuthenticationPrincipal User user 
) {
    order.setUser(user);

	...
}
```
장점: 타입 변환이 필요없고 Authentication과 동일하게 보안 특정 코드만 가진다.



SecurityContextHolder로 할 경우

```java
Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
User user = (User) authentication.getPrincipal();
```
SecurityContextHolder는 Context를 get하고 Authentication을 get한 후 Principal을 get해서 User에 담는다.





## Chapter5 구성 속성 사용하기


- 빈 연결: 스프링 애플리케이션 컨텍스트에서 빈으로 생성되는 애플리케이션 컴포넌트 및 상호 간에 주입되는 방법을 선언하는 구성
- 속성 주입: 스프링 애플리케이션 컨텍스트에서 빈의 속성값을 설정하는구성
ex) @Bean 애노테이션이 지정된 메서드는 사용하는 빈의 인스턴스를 생성하고 속성값도 설정한다.


DataSource 메서드로 사용할 경우 `EmbeddedDatabaseType`으로 Type(h2)을 정한 후 SQL이 실행되어 DB에 적용해도 된다.

그러나 스프링부트를 사용중일 때는 자동-구성이 DataSource 빈을 구성해주므로 dataSource()메서드가 필요없다.


H2 의존성 라이브러리를 classpath에서 찾아 사용할 수 있다면 빈을 자동으로 찾아 애플리케이션 컨텍스트에 생성하고
schema.sql, data.sql을 실행하여 DB에 적용시킨다.
어쩔 때(이름을 다르게 지정하고 싶거나 3개 이상을 지정해야 할 때)에는 구성 속성을 사용할 수 있다.



### 5.1 자동 구성 세부 조정(추상화의 개념과 yml)


추상화는 구성 가능한 모든 속성을 한 곳에서 관리하는 개념.
추상화함으로써 빈이 스프링 자체에서 해당 속성을 사용할 수 있게 해준다.



아래 속성 근원으로부터 원천 속성을 가져온다.

- JVM 시스템 속성
- 운영체제의 환경 변수
- 명령행 인자
- 애플리케이션의 속성 구성 파일


또, YML 파일에서 아래처럼 진행해놓으면,
톰캣의 JDBC 커넥션 풀을 classpath에서 자동으로 찾을 수 있다면 DataSource 빈이 그걸 사용한다.
그렇지 않다면 다른 커넥션 풀을 classpath에서 찾아 사용한다


```
spring:
  datasource:
    url: jdbc:mysql://localhost/tacocloud
    username: tacodb
    password: tacopassword


	schema:
		- 내가 원하는 스키마 이름.sql
	data:
		- ingredients.sql
```

원하는 경우 이렇게 schema, data도 사용 가능하다.
이런 명시적인 데이터 소스 구성 대신 JNDI(Java Naming and Directory Interface)에 구성하는 것을 원할 수도 있다.
→ 이걸 하면 다른 데이터 소스 구성 속성은 무시된다.

해당 yml 파일에서 HTTPS를 활성화하기 위해 server: ssl: key-store/key-store-password/key-password 도 사용 가능하다.



로깅하는 방법의 경우, logback.xml 파일을 사용할 수도 있지만,
application.yml에 logging 항목을 지정할 수 있다.
(기본 로그 파일 10MB)


```
logging:
	path: /var/logs/
	file: TacoLogs.log
	level:
		root: WARN
		org.springframework.security: DEBUG
```

yml파일에서 다른 속성의 값도 가져올 수 있다.

ex) greeting.welcome: ${spring.application.name}



### 5.2 페이징 & 구성 속성 생성하기


페이징


Paging이 어떻게 되는지 볼 수 있다.

스프링 데이터의 Pageable 인터페이스를 사용하면 페이지 번호와 크기로 결과의 일부분을 선택할 수 있다.

```java
@GetMapping
public String ordersForUser(@AuthenticationPrincipal User user, Model model){
    Pageable pageable = PageRequest.of(0, 20);
    model.addAttribute("orders", orderRepo.findByUserOrderByPlacedAtDesc(user, pageable));
    return "orderList";
}
```

근데 이렇게 20으로 하드코딩하면 좋지 않아서
아래처럼 코드가 변경된다. (기본값 = 20)


```java
@ConfigurationProperties(prefix = "taco.orders")
public class OrderController {
    private int pageSize = 20;
    public void setPageSize(int pageSize){this.pageSize = pageSize;}
	...
}
```

변경하고 싶을 때엔 아래처럼 yml에서 변경하면 된다.


```
taco:
  orders:
    pageSize:10
```

@ConfigurationProperties


구성 속성의 올바른 주입을 지원하기 위해 @ConfigurationProperties 어노테이션을 지원한다.
이 어노테이션은 구성 데이터의 홀더로 사용되는 빈에 지정되는 경우가 많고, 이렇게 사용하면
컨트롤러와 그 외 클래스 외부에 구성 관련 정보를 따로 유지할 수 있다.
여러 빈에 공통적인 구성 속성을 쉽게 공유할 수 있다.



그 예제를 한번 봐보자!



먼저 속성을 정의한 클래스를 만든다.
추가적으로 Validated 어노테이션을 사용해서 검증도 한번에 가능하다!


```java
@Component
@ConfigurationProperties(prefix = "taco.orders")
@Data
@Validated
public class OrderProps {
    
    @Min(value=5, message="must be between 5 and 25")
    @Max(value=25, message="must be between 5 and 25")
    private int pageSize = 20;
}
```

이 빈을 Controller에 주입한다.


```java
@Controller
@RequestMapping("/orders")
@SessionAttributes("order")
public class OrderController {

    private OrderRepository orderRepo;
    private OrderProps props;

    public OrderController(OrderRepository orderRepo, OrderProps props) {
        this.orderRepo = orderRepo;
        this.props = props;
    }
	...
	@GetMapping
	public String ordersForUser(@AuthenticationPrincipal User user, Model model){
    	Pageable pageable = PageRequest.of(0, props.getPageSize());			// 하드코딩 X
		...
	}
}
```

그래서 저렇게 Props 부분에 공통 속성들을 다 넣을 수 있다!!

(신기!)



구성 속성 메타데이터 선언


yml 파일의 taco: 부분에서 taco를 인식하지 못하면

해당 속성에 관한 메타데이터를 생성하는 것이 좋다.


```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
</dependency>
```
해당 의존성을 우선 추가해준다.



spring-boot-configuration-processor는 @ConfigurationProperties 어노테이션이 지정된 애플리케이션 클래스에 관한 메타데이터를 생성하는 어노테이션 처리기이다.

생성된 메타데이터는 yml 파일에서 자동완성 기능을 제공하고 속성의 문서를 보여주기 위해 사용된다.



커스텀 구성 속성에 관한 메타데이터를 생성하려면 json 파일을 생성하도록 한다.


```java
// additional-spring-configuration-metadata.json
{
  "properties": [{
    "name": "taco.orders.page-size",
    "type": "int",
    "description": "Sets the maximum number of orders to display in a list"
  }]
}
```



+ yml Profile



빈에 따른 활성화가 필요할 때는 @Profile을 사용한다.


```java
@Bean
@Profile({"dev", "qa})
public CommandLineRunner dataLoader(){ ... }
```



Chapter6 REST 서비스


지금까지 한 부분은 스프링 MVC를 활용해서 MPA(Multi Page Application)을 사용했다.

이제는 SPA(Single Page Application)로 프론트엔드를 구축할 것이라고 한다.



