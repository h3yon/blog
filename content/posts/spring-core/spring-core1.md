---
title: "스프링 핵심 원리 복습 일지#1"
date: 2022-04-14T23:56:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
---

# 스프링 핵심 원리(기본편)

생성일: 2022년 4월 10일 오후 5:00

## 0. 객체 지향 설계와 스프링

**EJB(Enterprise Java Beans)**

- 트랜잭션이 좋다. 분산 기술이 좋았다. 엔티티빈(JPA ORM)도 가지고 있었다.
- 비용이 비싸다. 어렵고 복잡하고 느리다(EJB에 의존적으로 코드를 짜야 한다, 컨테이너 띄우는 게 느리다).

→ 순수하게 자바로 돌아가자. (POJO Plain Old Java Object)

로드존슨이 EJB를 비판하면서 미래 스프링의 기틀을 다져준다.
링킹이 Hibernate 등장. 자바 표준 JPA 구현체로 사용될 수 있다.

JPA(인터페이스)

→ Hibernate, EclipseLink, 기타, ...

- 스프링 오픈소스 제안 → EJB란 겨울을 넘어 새로운 시작으로 지음

**스프링 역사**

2003~2006. (Spring Framework 1.0~2.0) - XML
2009~2013. (Spring Framework 3.0~4.0) - 자바
2014. Spring Boot 1.0 출시
2017. Spring Framework 5.0, Spring Boot 2.0(리액티브 프로그래밍 지원)
2020. Spring Framework 5.2, Spring Boot 2.3.x

**스프링**

[https://spring.io/projects](https://spring.io/projects)

- 핵심. 스프링 프레임워크
- 편의기능. 스프링 부트
- (선택)
스프링 데이터, -(MongoDB, NoSQL, Redis 기본 CRUD 지원)
스프링 세션, 스프링 시큐리티, 스프링 Rest Docs, 
스프링 배치,  - (천만명 데이터를 한번에 업데이트 힘듦. 천건씩 돌리고 저장하고. 배치)
스프링 클라우드
etc

**스프링 프레임워크와 스프링 부트**

| 스프링 프레임워크 | 스프링 부트 |
| --- | --- |
| 핵심: DI Container, AOP, event
웹: MVC, WebFlux
데이터 접근: Transaction, JDBC, ORM, XML
기술통합
테스트
언어 | (스프링 부트를 통해 스프링 프레임워크 사용하는 게 다수)
Tomcat 같은 웹서버 내장
starter 종속성 제공(손쉬운 빌드)
스프링과 써드 라이브러리 자동 구성(외부 라이브러리 버전)
production 준비 기능(상태 확인, 매트릭모니터링)
간결한 설정 |

**스프링의 핵심**

자바의 큰 특징 - **객체 지향** 언어.

객체지향 특징을 강력하게 살려내도록 도와주는 프레임워크

- **객체 지향 특징**
(**추상**이 **캡**이**다**)
    - 추상화
    - 캡슐화
    - 다형성 - 세상은 역할과 구현으로 이루어진다. (자동차 구현체: 아반떼, 테슬라 모델3, K3)
    실행 시점에 구현객체를 유연하게 변경할 수 있다.
    private Car car = new K3();
    → 스프링에서 IoC, DI는 다형성을 활용해 역할과 구현을 편리하게 다룰 수 있도록 지원한다
    - 상속

**SOLID**

- SRP: 단일 책임 원칙
한 클래스는 하나의 책임만 가져야 한다.
- OCP: 개방폐쇄 원칙
확장에는 열려 있으나 변경에는 닫혀 있어야 한다.
- LSP: 리스코프 치환 원칙
인터페이스와 구현체가 있다. 액셀은 앞으로 가야 한다는 규약이 있다.
구현체에서 액셀이 뒤로 가게 하면 LSP 위반이다.
- ISP: 인터페이스 분리 원칙
인터페이스 분리
- DI(Inversion)P: 의존관계 역전 원칙
인터페이스에 의존하라는 것. 즉, 역할과 구현에서 역할에 의존하라는 것.

→ OCP, DIP 중요!

**스프링!**

→ 다형성 + OCP, DIP를 가능하게 지원한다.

- DI(Dependency Injection 의존관계, 의존성 주입) Container 제공

**오늘의 정리**

- 역할과 구현, 인터페이스와 구현 클래스가 중요하다.
- 인터페이스 남발하면 추상화 비용이 발생하니까 리팩토링할 때 인터페이스 도입하는 것도 좋다.

## 1. 스프링 핵심 원리 이해1 - 예제 만들기

![spring-core1-0](https://user-images.githubusercontent.com/46602874/163417738-624f2698-6958-4b8c-ac91-48943d5c43fe.png)

실무에서는 HashMap → `ConcurrentHashMap`

프레임워크: JUnit처럼 내가 작성한 코드를 제어하고 대신 실행해준다.
라이브러리: 내가 작성한 코드가 제어의 흐름을 담당한다면 라이브러리

AppConfig처럼 객체를 생성하고 관리하면서 의존관계를 연결해주는 것은 **`IoC 컨테이너`** 혹은 **`DI 컨테이너`**라고 한다. 또는 어셈블러, **오브젝트 팩토리** 등으로 불리기도 한다.

프로그램의 제어 흐름을 외부에서 관리하게 하는 것을 IoC 라고 한다. ex) IoC

## 2. **스프링 핵심 원리 이해2 - 객체 지향 원리 적용**

- `@Configuration`: 설정 정보
- `@Bean`: 해당 어노테이션이 붙은 메서드를 모두 호출해서 반환된 객체를 `스프링 컨테이너에 등록`된다.
(`applicationContext.getBean`으로 **스프링 빈** 객체를 찾을 수 있다.)
    
    ![spring-core1-1](https://user-images.githubusercontent.com/46602874/163417747-88d8dd8a-7430-441b-a983-e09bf5092db3.png)
    
    아래 예시를 보면
    (왼쪽) Bean으로 등록했기 때문에 + 인터페이스에 구현체를 넣어줌으로써 원하는 `가격 정책`을 결정할 수 O
    (오른쪽) 사용처에서 알아서 가져와서 등록할 수 있음을 알 수 있다.
    
    ![spring-core1-2](https://user-images.githubusercontent.com/46602874/163417754-d68b71df-1627-4514-89ac-185969d29a98.png)
    

## 3. **스프링 컨테이너와 스프링 빈**

- 스프링 컨테이너
    
    ApplicationContext를 스프링 컨테이너라고 한다.
    
    BeanFactory는 ApplicationContext의 상위 인터페이스다.(최상위 인터페이스) - getBean 제공
    `ApplicationContext = BeanFactory 기능 + 여러 부가 기능`
    
    ```java
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
    1. 모든 빈 출력
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    
    2. 직접 등록한 빈인지 확인
    if(beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION)
    
    3. 빈 객체 조회
    ac.getBean(빈이름, 타입) / (타입)
    ex) ac.getBean("memberService",MemberService.class);
    
    4. 특정 타입 빈 모두 조회
    Map<String, MemberRepository> beansOfType =
    	ac.getBeansOfType(MemberRepository.class);
    
    5. 부모 타입으로 조회시, 자식이 둘 이상 있으면, 중복 오류가 발생
    assertThrows(NoUniqueBeanDefinitionException.class, () ->
    	ac.getBean(DiscountPolicy.class));
    
    6. 부모 타입으로 조회시, 자식이 둘 이상 있으면, 빈 이름을 지정하면 된다
    DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy",
    	DiscountPolicy.class);
    assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class);
    ```
    
    위에는 어노테이션 방식. 원한다면 아래처럼 xml 방식을 사용 가능
    
    ```java
    ApplicationContext ac = new
      GenericXmlApplicationContext("appConfig.xml");
    ```
    
- 스프링은 어떻게 xml, 어노테이션 설정 형식을 지원할까?
`BeanDefinition`이라는 `추상화`를 알아보자
→ `빈 설정 메타정보`, @Bean, <bean>당 각각 하나씩 메타 정보 생성
    
    ![spring-core1-3](https://user-images.githubusercontent.com/46602874/163417761-0ee59286-ac4a-4c46-9ede-6c7b3d9d900d.png)
    
    `AnnotatedBeanDefinitionReader`, `XmlBeanDefinitionReader`을 사용해서
    AppConfig.class를 읽고 `BeanDefinition`을 생성한다.
    
- BeanDefinition
    - BeanClassName
    - factoryBeanName (ex) appConfig)
    - factoryMethodName (ex) memberService)
    - Scope (기본값 = 싱글톤)
    - lazyInit (실제 빈을 사용할 때까지 최대한 생성을 지연처리하는 지에 대한 여부)
    - InitMethodName: 빈을 생성하고, 의존관계 적용 후 호출되는 초기화 메서드명
    - DestroyMethodName: 빈 생명주기 끝나고 제거하기 직전 호출되는 메서드명
    - Constructor arguments, Properties: 의존관계 주입에서 사용된다.
    
    ```java
    String[] beanDefinitionNames = ac.getBeanDefinitionNames();
    ```
    

## 4. **싱글톤 컨테이너**

![spring-core1-4](https://user-images.githubusercontent.com/46602874/163417764-581a8eb2-07e1-4ece-aa12-5b5dedb2ff18.png)

요청 올 때마다 서비스를 만들면 무리가 될 수 있다.(메모리 낭비)

### 싱글톤 패턴

클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.

`private 생성자`를 사용해서 외부에서 new를 사용하지 못하게 한다.
사용 방법은 아래와 같다.

```java
public class SingletonService {
	//1. static 영역에 객체를 딱 1개만 생성
	private static final SingletonService instance = new SingletonService();

	//2. 이 static 메서드를 통해서만 조회하도록 허용
  public static SingletonService getInstance() {
      return instance;
	}

	//3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다. 
	private SingletonService() {}

	public void logic() { System.out.println("싱글톤 객체 로직 호출");
	} 
}
```

**싱글톤 패턴 문제점**

- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어간다.
- 의존관계상 클라이언트가 구체 클래스에 의존한다. DIP를 위반한다.
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 테스트하기 어렵다. → 안티패턴
- 서버 환경에서는 싱글톤이 1개만 생성됨을 보장하지 못한다.
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 있어서 상속이 불가능하다.
- 결론적으로 유연성이 떨어진다.
- 안티패턴으로 불리기도 한다.

### 싱글톤 컨테이너

`스프링 컨테이너`는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로
관리한다.
학습한 스프링 빈 = 바로 싱글톤으로 관리되는 빈

- 스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도, `객체 인스턴스를 싱글톤`으로 관리
    
    ![spring-core1-5](https://user-images.githubusercontent.com/46602874/163417771-a197f5c5-792d-422e-8aff-fe9cde040fac.png)
    
    → 싱글톤 컨테이너 역할
    → 지저분한 코드가 들어가지 않아도 된다
    → `DIP, OCP, 테스트, private 생성자`로 부터 자유롭게 싱글톤을 사용 가능
    : 학습한 memberService, memberRepository 싱글톤 가능
    

### 주의점

- `stateless`로 설계해야 한다.
    - 의존적인 필드가 있으면 안 된다.
    - 클라이언트가 값을 변경할 수 있는 필드가 있으면 안 된다.
    - 가급적 `읽기만 가능하게` 해야 한다.
    - 필드 대신, 자바에서 공유되지 않는 `지역변수`, `파라미터`, `스레드로컬` 등을 사용해야 한다.
    →왜? `공유필드`를 조심해야 한다.(동시성 문제)
    예를 들어 사용자A의 주문금액은 10000원이 되어야 하는데, 20000원이라는 결과가 나올 수 있다.

### **@Configuration과 싱글톤**

아래 코드를 봤을 때, memberRepository가 2개 사용되니까 싱글톤이 깨지는 것처럼 볼 수도 있다.

```java
@Configuration
  public class AppConfig {
      @Bean
      public MemberService memberService() {
          return new MemberServiceImpl(memberRepository());
      }
      @Bean
      public OrderService orderService() {
          return new OrderServiceImpl(
                  memberRepository(),
                  discountPolicy());
			}
      @Bean
      public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
      }
... }
```

위에 언급했듯, `스프링 컨테이너 = 싱글톤 레지스트리`
→ 스프링 빈이 `싱글톤`이 되도록 보장해주어야 한다. 
그래서 모든 비밀은 `@Configuration` 을 적용한 AppConfig 에 있다. → 스프링 빈이 된다.

```java
@Test
void configurationDeep() {
  ApplicationContext ac = new
		  AnnotationConfigApplicationContext(AppConfig.class);
	//AppConfig도 스프링 빈으로 등록된다.
	AppConfig bean = ac.getBean(AppConfig.class);
	System.out.println("bean = " + bean.getClass());
	//출력: bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$bd479d70
}
```

@Configuration 을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해서 싱글톤을 보장!

아마 CGLIB이 아래처럼 해줄 것

```java
@Bean
public MemberRepository memberRepository() {
	if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) { 
		return 스프링 컨테이너에서 찾아서 반환;
	} else { //스프링 컨테이너에 없으면
		기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록 return 반환
	} 
}
```

### **@Configuration 을 적용하지 않고, @Bean 만 적용하면 어떻게 될까?**

```java
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
call AppConfig.memberRepository

memberService -> memberRepository =
	hello.core.member.MemoryMemberRepository@6239aba6
orderService -> memberRepository  =
	hello.core.member.MemoryMemberRepository@3e6104fc
memberRepository = 
	hello.core.member.MemoryMemberRepository@12359a82
```

각각 다 다른 MemoryMemberRepository를 가진다. → 싱글톤 X

- @Bean만 사용해도 `스프링 빈으로 등록`되지만, `싱글톤을 보장하지 않는다`.
memberRepository() 처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않는다.

## 5. 컴포넌트 스캔

스프링은 설정 정보가 없어도 `자동으로 스프링 빈을 등록`하는 컴포넌트 스캔이라는 `기능`을 제공한다.

컴포넌트 스캔을 사용하려면 `ComponentScan`을 붙여주면 된다.

```java
@Configuration
@ComponentScan(
        excludeFilters = @Filter(type = FilterType.ANNOTATION, classes =
Configuration.class))
public class AutoAppConfig {
}
```

위 예제에서는 @Configuration이 붙은 설정 정보가 자동으로 등록되어서, 

`excludeFilters`를 사용하여 컴포넌트 스캔 대상에서 제외 가능
`basePackages` 탐색할 분위기의 시작 위치 지정 가능
`basePackageClasses` 지정한 클래스의 패키지를 탐색 시작 위치로 지정

`@Autowired` 는 의존관계를 자동으로 주입 가능

```java
@Component
  public class OrderServiceImpl implements OrderService {
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
		@Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy
															discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```

`@Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다.

### 컴포넌트 스캔 기본 대상

- @Component
- @Controller
- @Service
- @Repository
- @Configuration: 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리

> 필터
• useDefaultFilters: 이 옵션을 끄면 기본 스캔 대상들 제외
• includeFilters: 스캔 대상 추가 지정
• excludeFilters: 스캔 제외 대상 추가 지정
> 

스캔 대상에 추가/제외할 애노테이션

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {
}

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}

@Configuration
@ComponentScan(
    includeFilters = @Filter(type = FilterType.ANNOTATION, 
					classes = MyIncludeComponent.class),
    excludeFilters = @Filter(type = FilterType.ANNOTATION, 
					classes = MyExcludeComponent.class)
)
static class ComponentFilterAppConfig {
}
```

FilterType은 5가지 옵션이 있다.

- ANNOTATION
- ASSIGNABLE_TYPE
- ASPECTJ
- REGEX
- CUSTOM

스프링 빈이 등록될 때, 빈 이름이 같은 게 2개 있을 경우(`자동빈 vs 자동빈`), 
`ConflictingBeanDefinitionException` 예외 발생 가능

```java
@Bean(name = "memoryMemberRepository")
public MemberRepository memberRepository() {
   return new MemoryMemberRepository();
}
```

`자동 빈 vs 수동 빈` 일 경우, 수동빈 등록이 우선권을 가져, `자동 빈을 오버라이딩` 한다.

