---
title: "[스프링 인 액션5]Chapter1~3 내용 정리"
date: 2021-10-14T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring in action
  - spring
---


# [스프링 인 액션5] Chapter1~3 내용 정리

스프링 인 액션에 대한 내용을 읽으면서  
중요 내용, 한번 더 짚고 넘어가면 좋을 내용에 대해서 써보려고 한다.  

# Chapter1

## SpringBootApplication

애플리케이션의 부트스트랩 클래스에,  
SpringBootApplication이 있다는 건 다들 봤을 것이다.  

이 SpringBootApplication이 어떤 어노테이션이 결합한 것인지 보자

- SpringBootConfiguration
    현재 클래스를 구성 클래스로 지정한다.(@Configuration)의 특화된 형태

- EnableAutoConfiguration
     스프링 부트 자동-구성을 활성화한다

- ComponentScan
    컴포넌트 검색을 활성화한다.
    @Component, @Controller, @Service 등의 애노테이션과 함께 클래스를 선언할 수 있게 한다.

- 여기서 잠깐!
    @Repository, @Controller 같은 어노테이션을 `스테레오타입 애놑테이션`이라고 한다.
    스프링에서 주로 사용하는 역할 그룹을 나타낸다.

그럼 해당 어노테이션이 붙은 클래스에,  
main 메서드가 있는데, 이 메서드는  
`Jar 파일이 실행될 때 호출되어 실행되는 메서드`이다.  

main 메서드 안에는 SpringApplication.run(ProjectApplication.class, args)가 있는데,  
run 메서드에는 구성 클래스와, 명령행 인자가 들어간다.  

애플리케이션을 실행하면 우리가 작성한 코드가 돌아감을 알 수 있다.  

```
2021-10-14 11:08:02.834  INFO 9586 --- [  restartedMain] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-10-14 11:08:02.847  INFO 9586 --- [  restartedMain] com.sia.tacos.TacosApplication           : Started TacosApplication in 22.183 seconds (JVM running for 28.003)
2021-10-14 11:08:38.870  INFO 9586 --- [nio-8080-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-10-14 11:08:38.870  INFO 9586 --- [nio-8080-exec-1] o.s.web.servlet.DispatcherServlet        : Initializing Servlet 'dispatcherServlet'
```

실행 후 로그를 보면 Tomcat이 실행된다.  
tomcat을 설치하지 않았음에도 실행이 가능한 이유는 뭘까?  
`SpringBootApplication`이 실행에 필요한 모든 것을 포함하고 있기 때문이다.  

## 컨트롤러 테스트

테스트 파일에서 붙은 어노테이션의 경우 `SpringBootTest`가 있다.  
`@WebMvcTest`어노테이션도 있는데,  
이건 스프링 부트에서 제공하는 특별한 테스트 어노테이션이다.  

스프링 MVC 애플리케이션 형태로 테스트가 실행되도록 하는데,  
실제 서버를 시작하는 대신, 스프링 MVC의 모의 메커니즘을 사용해도 충분할 때가 있다.  
그럴 때 테스트 클래스에 MockMvc 객체를 주입한다.  

## DevTools

스프링부트에서 DevTools는 그냥 개발자를 위한 도구인 줄 알았다.  
근데, DevTools는 유용한 기능이란 걸 알았다.

- 코드 변경 시 Application 재시작
- 리소스 변경 시 브라우저 새로고침
- 템플릿 캐시 자동으로 비활성화
- H2 DB 사용 시 H2 콘솔 활성화

## 스프링 살펴보기

- 스프링 데이터
    스프링 데이터는 간단한 인터페이스로 애플리케이션의 데이터 리퍼지토리를 정의할 수 있다. 데이터를 저장하고 읽는 메서드를 작명 규칙을 사용해서 정의한다.
    게다가, 서로 다른 종류의 데이터베이스와 함께 사용될 수 있다.(JPA, Mongo, Neo4j 등)

- 스프링 시큐리티
    인증, 허가, API 보안을 포함하는 보안 요구를 다룬다.

- 스프링 통합과 배치
    서로 다른 컴포넌트를 통합할 필요가 생길 때, 이러한 요구사항을 해결하기 위한 패턴이 있다. 스프링 통합과 스프링 배치는 이런 패턴 구현을 제공한다.
    `스프링 통합`은 데이터가 사용 가능한 즉시 처리되는 실시간 통합을 한다.
    `스프링 배치`에서는 다량의 데이터가 처리되는 시점을 트리거가 알려줄 때 배치 통합을 처리해준다.

- 스프링 클라우드

# Chapter2

## 유효성 검사 규칙

Order 등 엔티티 같은 클래스에 이렇게 유효성 검사가 가능하다.

```java
    @NotBlank(message = "Zip code is required")
    private String deliveryZip;

    @CreditCardNumber(message = "Not a valid credit card number")
    private String ccNumber;

    @Pattern(regexp = "^(0[1-9]|1[0-2]|)([\\/])([1-9][0-9])$",
            message = "Must be formatted MM/YY")
    private String ccExpiration;

    @Digits(integer = 3, fraction = 0, message = "Invalid CVV")
    private String ccCVV;
```

NotNull 외에도 NotBlack, CreditCardNumber, Pattern, Digits도  
가능하다는 걸 알 수 있었다.

## 자바 빈 유효성 검사

위처럼 잘 진행했다면 아래처럼  
`@Valid`를 넣어서 유효성 검사를 수행한다.

```java
    @PostMapping
    public String processDesign(@Valid Taco design, Errors errors) {
        if(errors.hasErrors()){
            return "design";
        }

        log.info("Processing design: " + design);
        return "redirect:/orders/current";
    }
```

## 뷰 컨트롤러

HomeController와 같이 모델 데이터나 사용자 입력을 처리하지 않는 간단한 컨트롤러의 경우,  
다른 방법으로 컨트롤러를 정의할 수 있다.  

뷰에 요청을 전달하는 일만 하는 컨트롤러를 `뷰 컨트롤러`라고 한다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry){
        registry.addViewController("/").setViewName("home");
    }
}
```

그럼 위의 코드를 살펴볼 수 있다.

- `WebConfig`: 뷰 컨트롤러 역할을 수행하는 구성 클래스! 여기서 중요한 것은 `WebMvcConfigurer`을 구현한다는 것
- `WebMvcConfigurere`: 
        스프링 MVC를 구성하는 메서드를 정의한다. 정의된 모든 메서드의 기본적인 구현을 제공한다. 그렇기 때문에 필요한 메서드만 오버라이딩만 하면 된다.
        그래서 위 코드에선 필요한 addViewControllers()만 오버라이딩한다.
        `addViewControllers` 메서드는 하나 이상의 뷰 컨트롤러를 등록하기 위해 사용할 수 있는 `ViewControllerRegistry`를 인자로 받는다.
- registry.addViewController("/").setViewName("home");
        위 코드에서의 이 부분을 봐보자.
        "/"를 인자로 전달하여 addViewController()를 호출한다.
        "/" 경로의 요청이 전달되어야 하는 뷰로 home을 지정하기 위해서,
        ViewControllerRegistry 객체를 반환하고 그 부분의 setViewName()을 호출한다.
        그럼 이제 homeController를 삭제해도 똑같이 잘 보인다.

## 템플릿 캐싱

템플릿은 최초 사용될 대 한번만 파싱된다.  
파싱된 결과는 사용을 위해 캐시에 저장된다.  
매번 요청 처리시마다 불필요하게 템플릿 파싱 하지 않아 성능 향상에 좋다.  

하지만 개발 시 바꾼 걸 보려면 애플리케이션을 새로 설치해야 한다.  
그래서 캐싱 속성만 잠시 spring.thymeleaf.cache=false로 한다.

# Chapter3 (JDBC)

JDBC를 잠시 살펴보자.  
`queryForObject` 메서드는 쿼리를 수행하고,  
그 결과를 `mapRowToIngredient` 메서드 사용하여 Ingredient 객체로 생성하는 것에 초점을 둔다.  

## JDBC 사용 예제

그럼 사용 예제를 보고 대충 이해해보자

```java
public class JdbcUserRepository implements UserRepository{

    private JdbcTemplate jdbc;

    public JdbcUserRepository(JdbcTemplate jdbc){
        this.jdbc = jdbc;
    }

    @Override
    public Iterable<User> findAll() {
        return jdbc.query("select id, name from User", this::mapRowToUser);
    }

    @Override
    public User findById(String id) {
        return jdbc.queryForObject("select id, name from User where id=?", this::mapRowToUser, id);
    }

    @Override
    public User save(User user) {
        jdbc.update("insert into User (id, name) values (?, ?)",
                user.getId(), user.getName());
        return user;
    }

    private User mapRowToUser(ResultSet rs, int rowNum) throws SQLException {
        return new User(
                rs.getString("id"),
                rs.getString("name"),
        );
    }
}
```

보면 알겠지만,  
select문의 경우, 다수일 땐 `jdbc.query`,  
하나일 땐, `jdbc.queryForObject`를 사용함을 알 수 있다.  
매개변수로는 `"쿼리", (출력 내용), (? 안에 들어갈 내용)`임을 알 수 있다.  
update일 때에는 `jdbc.update(쿼리, 인자)`가 들어간다.  

## 작성한 sql 문

우리가 미리 이 정보는 만들어질 때 있었으면 좋겠다 하는 경우,   
insert문이나, ddl에 대한 sql문을 미리 작성해놓을 수 있다.   

작성하는 것은 알고 있었는데,  
어디에 어떻게 저장되어서 작동하는지를 한번 알아보려고 한다.  

.sql 파일 같은 경우, `resources` 폴더에 저장한다.  
그러면 애플리케이션이 시작될 때 데이터베이스에서 자동 실행된다.  

## 이해 안 됐던 부분(PreparedStatementCreator, keyholder)

jdbc 코드를 구현하면서, 아래 부분이 정말 이해되지 않았다.  
그래서 이 부분을 살펴보려고 한다.

```java
    private long saveTacoInfo(Taco taco) {
        taco.setCreatedAt(new Date());
        PreparedStatementCreator psc =
                new PreparedStatementCreatorFactory("insert into Taco (name, createdAt) values (?, ?)",
                        Types.VARCHAR, Types.TIMESTAMP
                ).newPreparedStatementCreator(
                        Arrays.asList(
                                taco.getName(),
                                new Timestamp(taco.getCreatedAt().getTime())));

        KeyHolder keyHolder = new GeneratedKeyHolder();
        jdbc.update(psc, keyHolder); // 요기
        return keyHolder.getKey().longValue();
    }
```

이 메서드 같은 경우는 타코 ID가 필요한데,  
그 부분을 알 수 없어 update() 메서드가 사용된다.  

update()메서드는 `PreparedStatementCreator` 객체와  
`KeyHolder` 객체를 인자로 받는다.  

이 `KeyHolder`가 생성된 타코 ID를 제공한다.  
keyholder를 사용하기 위해서는 `PreparedStatementCreator`도 생성해야 한다.  

- PreparedStatementCreator 생성과정
        생성 과정이 너무 길어서 따로 한번 살펴보자.  
        위에 보면, `PreparedStatementCreatorFactory` 객체를 생성한다.  
        매개변수로는 쿼리, Type이 들어가고,  
        그 다음, `newPreparedStatementCreator`를 생성한다.

        keyHolder를 생성하고나서야 update문을 호출할 수 있다.

이 부분은 더 보면서 이해해야 할 것 같다.

## SessionAttribute

기존 컨트롤러에서 관련 작업을 해준다.  
코드를 봐보자  

```java
@Controller
@RequestMapping("/design")
@SessionAttributes("order") // 이 부분 추가
public class DesignTacoController {

    ...

    @ModelAttribute(name = "order")
    public Order order(){
        return new Order();
    }
    
    @ModelAttribute(name = "taco")
    public Taco taco(){
        return new Taco();
    }

    ...

    @PostMapping
    public String processDesign(@Valid Taco design, Errors errors, @ModelAttribute Order order) {
        if(errors.hasErrors()){
            return "design";
        }
        
        Taco saved = tacoRepo.save(design);
        order.addDesign(saved);

        return "redirect:/orders/current";
    }

```

기존 코드와 다르게, SessionAttributes/ModelAttribute 어노테이션이 추가되었다.  
SessionAttributes의 경우,  
`다수의 타코를 생성하고 그걸 하나의 주문으로 추가할 수 있게 하기 위해서`라고 한다  
하나의 세션에서 생성되는 Taco 객체와 주문은 다수의 http 요청에 걸쳐 존재해야 된다.  
(이 부분 한번 더)  

@ModelAttribute는 이 매개변수의 값이 모델로부터 전달되어야 한다.  
위 코드 processDesign 부분을 보면,  
세션에 보존된 Order에 Taco 객체를 추가한다.  

## SimpleJdbc (PreparedStatementCreator 대신)

관련 id를 알 수 없다보니까,  
Keyholder와 PreparedStatementCreator를 사용했었다.  
이거 대신 SimpleJdbcInsert를 사용해보자.  

예시 코드는 아래와 같다.  

```java
@Repository
public class JdbcOrderRepository {
    private SimpleJdbcInsert orderInserter;
    private SimpleJdbcInsert orderTacoInserter;
    private ObjectMapper objectMapper;

    public JdbcOrderRepository(JdbcTemplate jdbc) {
        this.orderInserter = new SimpleJdbcInsert(jdbc)
                .withTableName("Taco_Order")
                .usingGeneratedKeyColumns("id");
        
        this.orderTacoInserter = new SimpleJdbcInsert(jdbc)
                .withTableName("Taco_Order_Tacos");
        
        this.objectMapper = new ObjectMapper();
    }
}
```

JdbcTemplate을 사용해서 2개의 SimpleJdbcInsert 인스턴스를 생성함을 알 수 있다.  
orderInserter에는 주문 데이터를 추가,  
orderTacoInserter에는 해당 주문 id 및 관련 id를 추가하기 위해 구성된다.  
근데 위에꺼와 다르게 어떤 id값을 해당 테이블에 생성할지는 지정하지 않는다.  
(우리가 지정하기 때문)  

SimpleJdbcInsert는 `execute()`, `executeAndReturnKey()`를 가진다.  
두 메서드는 모두 `Map<String, Object>` 를 인자로 받는다.

```java
	private long saveOrderDetails(Order order) {
		@SuppressWarnings("unchecked")
		Map<String, Object> values =
		objectMapper.convertValue(order, Map.class);
		values.put("placedAt", order.getPlacedAt());
		long orderId =
				orderInserter
				.executeAndReturnKey(values)
				.longValue();
		return orderId;
	}
```

사용 예시를 보자.
위의 메서드를 바탕으로 orderId를 도출해낼 수 있다.

## JPA

`NoArgsConstructor`를 보자.  
의미는 다들 알고 있을 거라고 가정하고 보면,  
간혹가다 아래처럼 되어 있는 걸 볼 수 있다.

```java
@NoArgsConstructor(access = AccessLevel.PRIVATE, force = true)
@Entity
public class Test{
    
    @Id
    @GeneratedValue(strategy=GenerationType.AUTO)
    private Long id;

    private Date createdAt;

    @PrePersist
    void createdAt(){
        this.createdAt = createdAt;
    }
}
```

이 부분의 경우, 인자 없는 생성자의 사용을 원치 않으므로,  
access 속성을 `AccessLevel.PRIVATE`로 설정해놓았음을 알 수 있다.  
또 초기화가 필요한 final 속성이 있으므로 force 속성을 true로 설정함을 알 수 있다.  
그래서 Lombok이 그 속성들을 null로 설정한다.  

`@PrePersist`의 경우, 해당 Test 객체가 저장되기 전에  
createdAt을 현재 일자,시간으로 설정한다.

## Serializable

```java
@Data
@Entity
@Table(name="Taco_Order")
public class Order implements Serializable {

    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private Date placedAt;
}
```

위의 예시를 보면 implements Serializable을 하는 것을 알 수 있다.  
이 부분이 궁금해서 검색한 부분을 따라 써본다!  
 
자바는 내부적으로 오브젝트(또는 Reference) 형식의 데이터를 많이 사용하고,  
오브젝트 안에 내부적으로 다른 오브젝트를 참조할 수 있는 주소값이 담길 수 있다.  

주소값의 실체를 다 끌어와서 Primitive 한 값 형식 데이터로 전부 변조하는 작업을 바로 직렬화(Serialization)라고 한다.
직렬화 된 데이터 형식은 언어에 따라 텍스트로 된 데이터 또는 바이너리 등의 모양을 띄게 되고, 오브젝트 타입이 없다.  
모든것이 Primitive 한 값 형식의 데이터 묶음이며, 이것은 파일 저장이나 네트워크 전송시 파싱 할 수 있는 유의미한 데이터가 되는 것이라고 한다.  

(출처: https://okky.kr/article/224715)  

> JDBC와 다른 것 중 하나 알아보기

Jdbc를 사용했을 때는 resources 디렉토리 안에 sql 파일을 넣었다.  
그러면 자동으로 추가되었었는데,  
JPA를 사용하면 그렇지 않기 때문에,  
`CommandLineRunner`를 사용해서 추가해준다.(상황에 따라 다름)  

## DSL(Domain Specific Language)

JPA를 사용하면 레퍼지토리에  
findByEmail 이런 게 사용 가능하다.  

본질적으로 스프링 데이터는 일종의 DSL(Domain Specific Language)을  
정의하고 있어서 persistence에 대한 내용이 repository 메서드 시그니처에 표현된다.  




