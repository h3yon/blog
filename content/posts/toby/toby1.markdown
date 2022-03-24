---
title: "[토비의 스프링] 정리"
date: 2021-11-01T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
  - toby
---


## 토비의 스프링 2차 정리

### 관심사의 분리?

관심이 같은 것끼리는 하나의 객체 안으로 또는 친한 객체로 모이게 하고   
관심이 다른 것은 가능한 한 따로 덜어져서 서로 영향을 주지 않도록 분리하는 것

### 템플릿 메소드 패턴

슈퍼클래스에 기본적인 로직의 흐름을 만들고    
기능의 일부를 추상 메소드나 오버라이딩이 가능한 protected 메소드 등으로 만든 뒤   
서브 클래스에서 이런 메소드를 필요로 맞게 구현해서 사용   

+ 훅메소드? 슈퍼클래스에서 디폴트 기능을 정의해두거나 비워뒀다가 선택적으로 오버라이드할 수 있도록 만들어둔 메소드

### 팩토리 메소드 패턴

서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는 것   
이 메소드는 주로 인터페이스 타입으로 오브젝트를 리턴    
즉, 슈퍼클래스의 기본 코드에서 독립시키는 방법   

### 디자인패턴

소프트웨어 설계 시 특정 상황에서 자주 만나는 문제를 해결하기 위해 사용할 수 있는 재사용 가능한 솔루션   
의도에 초점을 맞추자!

### 개방폐쇄원칙

클래스나 모듈은 확장에는 열려 있어야 하고 변경에는 닫혀 있어야 한다.

### 전략패턴

디자인 패턴의 꽃. OCP에도 가장 잘 맞고.   
변경이 필요한 알고리즘을 인터페이스를 통해 외부로 분리시키고   
구체적인 알고리즘 클래스를 필요에 따라 바꿔서 사용할 수 있게 하는 디자인 패턴.   
이를 대체 가능한 전략으로 보기 때문에 패턴 이름이 전략 패턴!   


## 제어의 역전

### 팩토리

분리시킬 기능을 담당할 클래스!   
이 클래스의 역할은 객체 생성방법을 결정하고 그렇게 만들어진 오브젝트를 돌려주는 것인데,   
이런 일을 하는 오브젝트를 흔히 팩토리   
→ Component 역할을 하는 오브젝트와 어플리케이션의 구조를 결정하는 오브젝트를 분리했다는 데 의미가 있다.

### IoC 제어관계 역전

모든 종류의 작업을 사용하는 쪽에서 제어하는 구조   

### 라이브러리와 프레임워크 차이

라이브러리: 어플리케이션 코드는 애플리케이션 흐름을 직접 제어. 동작 중 필요하면 능동적으로 사용   
프레임워크: 어플리케이션 코드가 프레임워크에 의해 사용   

### 빈, 애플리케이션 컨텍스트, 빈팩토리 등 단어 설명



| 단어                   | 설명                                                         |
| :--------------------- | :----------------------------------------------------------- |
| 빈                     | 스프링이 IoC 방식으로 관리하는 오브젝트. 스프링이 직접 생성과 제어를 담당하는 오브젝트만을 빈이라고 함 |
| 빈 팩토리              | 스프링의 IoC를 담당하는 핵심 컨테이너. 빈을 등록하고 생성하고 조회하고 도려주고 등 관리하는 기능 담당. getBean()과 같은 메서드 정의 |
| 애플리케이션 컨텍스트  | 빈팩토리를 확장한 IoC 컨테이너 빈팩토리의 기본적인 기능 + 스프링이 제공하는 각종 부가 서비스 제공 → BeanFactory 상속 |
| 설정정보/ 메타정보     | applicationContext / 빈팩토리가 IoC를 적용하기 위해 사용하는 메타 정보 영어로 Configuration, 구성 정보 내지는 형상 정보라는 의미 |
| 컨테이너/ IoC 컨테이너 | IoC 방식으로 빈을 관리한다는 의미에서 applicationContext나 Bean Factory를 컨테이너/IoC 컨테이너라고 함 |
| 스프링 프레임워크      | IoC 컨테이너, 애플리케이션 컨텍스트를 포함해서  스프링이 제공하는 모든 기능을 통틀어 말할 때 주로 사용 |

@Configuration:   
빈팩토리를 위한 오브젝트 설정을 담당하는 클래스로 인식할 수 있게 함.   
ApplicationContext가 활용하는 IoC 설정정보   

@Bean:    
오브젝트 만들어주도록, 오브젝트를 생성하고 초기화해서 돌려주는 거니까.  
ApplicationContext는 Bean이 붙은 메소드의 이름을 가져와 빈 목록을 만들어둔다.   


## 싱글톤 레지스트리와 오브젝트 스코프

### 동일성과 동등성

동일성 비교: 두개의 오브젝트가 완전히 동일한 오브젝트 (==)  
동등성 비교: 동일한 정보를 담고 있는 오브젝트 (equals)  

### 애플리케이션 컨텍스트 == 싱글톤 레지스트리

애플리케이션 컨텍스트는 IoC 컨테이너.  
동시에 싱글톤을 저장하고 관리하는 싱글톤 레지스트리  

### 싱글톤 패턴 & 구현 방법

싱글톤 패턴 = 한 개의 오브젝트만 만들어서 사용  

- 생성자를 private!
- 생성된 싱글톤 오브젝트를 저장할 수 있는 자신과 같은 타입의 static 필드 정의
- 스태틱 팩토리 메서드인 **getInstance()**를 만들고 이 메소드가 최초로 호출되는 시점에서 한번만 오브젝트가 만들어지게 한다.
  생성된 오브젝트는 스태틱 필드에 저장된다. 또는 스태틱 필드의 초기값으로 오브젝트를 미리 만들어둘 수도 있다.

### 싱글톤 == 안티패턴 이유?

- private 생성자를 가지고 있어서 상속할 수 없다.
  객체지향의 장점인 **상속**과 이를 이용한 **다형성**을 적용할 수 없다.
- 싱글톤은 테스트하기가 어렵다.
  만들어지는 방식이 제한적이어서 mock 오브젝트 등으로 대체하기 어렵다.
  싱글톤은 초기화 과정에서 생성자 등을 통해 사용할 오브젝트를 다이내믹하게 주입하기도 힘들어서 직접 오브젝트를 만들어 사용할 수밖에 없다.
  테스트용 오브젝트로 대체하기 힘들다.
- **서버환경에서는 싱글톤이 하나만 만들어지는 것을 보장하지 못한다.**
  클래스 로더를 어떻게 구성하고 있느냐에 따라서 하나 이상의 오브젝트가 만들어질 수 있다.
- 싱글톤의 사용은 전역 상태를 만들 수 있기 때문에 바람직하지 못하다.
  **static 메서드를 이용**해서 언제든지 싱글톤에 쉽게 접근할 수 있기 때문에 **자연스럽게 전역상태로 사용되기 쉽다**.
  아무 객체나 자유롭게 접근하고 수정하고 공유할 수 있는 전역 상태를 갖는 것은 객체 지향 프로그래밍에서는 권장되지 않는 프로그래밍 모델

### 싱글톤 레지스트리

기본적인 싱글톤 패턴의 구현방식은 여러 개의 단점이 있어서,   
**직접 싱글톤 형태의 오브젝트를 만들고 관리하는 기능**을 제공하는 것  

**싱글톤 레지스트리의 장점**은 스태틱 메서드와 private 생성자를 사용해야 하는 클래스가 아니라  
평범한 자바 클래스를 싱글톤으로 활용하게 해준다는 점이다.  

**싱글톤 레지스트리 덕분에**, 싱글톤 방식으로 사용될 애플리케이션 클래스라도 **public 생성자**를 만들 수 있다.  
싱글톤으로 사용돼야 하는 환경이 아니라면, 간단히 오브젝트를 생성할 수 있다.  
→ 테스트 환경에서 오브젝트를 만들 수도 있고, 목 오브젝트로 대체도 가능.  

**스프링은** IoC컨테이너일 뿐만 아니라, 고전적인 싱글톤 패턴을 대신해서,  
싱글톤을 만들고 관리해주는 **싱글톤 레지스트리라는** 점을 기억!  

### 빈의 스코프

빈의 기본 스코프 = 싱글톤  

경우에 따라서는 프로토타입 스코프,  
→ 컨테이너에 빈을 요청할 때마다 매번 새로운 오브젝트를 만들어준다.  

요청 스코프 = HTTP 요청이 생길 때마다 생성   

세션 스코프 = 웹의 세션과 스코프가 유사한 스코프  

## 의존관계 검색과 주입

검색 방식은 아래와 같다  

```
this.connectionMaker = context.getBean("connectionMaker", ConnectionMaker.class); // 의존관계 검색 방식
```

의존관계 검색 방식은 기존 의존관계 주입의 거의 모든 장점을 갖고 있다!  
오브젝트 팩토리 클래스나 스프링 API를 이용하는 코드가 섞여 있는 건 어색해서 의존관계 주입 방식을 사용하는 게 낫다.  

그런데, 의존관계 검색 방식을 사용해야 할 때가 있다.  
서블릿에서 스프링 컨테이너에 담긴 오브젝트를 사용하려면 의존관계 검색 방식을 사용해서 오브젝트를 가져와야 함.  
다행히 이런 서블릿은 스프링이 만들어서 제공하기 때문에 직접 구현할 필요 X  

## XML을 이용한 설정

DaoFactory = DI 작업에 참고하는 일종의 참고 정보  
DI 구성이 바뀔 때마다 자바 코드를 수정하고 컴파일 하는 것도 귀찮은 작업  

그 중에서 XML로 DI 의존관계 설정 정보를 만들 수 O  
컴파일과 같이 별도의 빌드 작업이 없다는 것도 장점  
오브젝트의 관계가 바뀌어도 바르게 변경사항 반영 가능.  

## 테스트

### 픽스처

테스트 수행하는 데 필요한 정보나 오브젝트
일반적으로 픽스처는 여러 테스트에서 반복적으로 사용되기 때문에 @Before 메소드 이용해 생성

테스트는 가능한 한 독립적으로 매번 새로운 오브젝트를 만들어서 사용하는 것이 원칙
하지만 생성에 많은 시간/자원이 소모되어서 테스트가 공유하는 오브젝트를 만들기도 함.

빈은 싱글톤으로 만들어서 상태를 갖지 않는다.
그래서 애플리케이션 컨텍스트는 한번만 만들고 공유해서 사용해도 된다.

### 어노테이션

- @RunWith(SpringJUnit4ClassRunner.class) 
     -> 스프링 테스트 컨텍스트 프레임워크의 JUnit 확장기능 지정
- @ContextConfiguration(locations="/applicationContext.xml") 
     -> 테스트 컨텍스트가 자동으로 만들어줄 애플리케이션 컨텍스트 위치 지정
- @Autowired
     -> `@Autowired`가 붙은 인스턴스 변수가 있으면,
        테스트 컨텍스트 프레임워크는 변수 타입과 일치하는 컨텍스트 내의 빈을 찾는다.
        빈을 자동으로 가져올 수 있는 방법을 타입에 의한 자동와이어링

### JUnit, 스프링 테스트 컨텍스트

JUnit은 테스트 메소드를 수행할 때마다 새로운 오브젝트를 만든다고 한다 → 이걸 테스트  
JUnit과 반대로, 스프링 테스트용 애플리케이션 컨텍스트는 테스트 개수에 상관 없이 한개만 생성.  

## 템플릿

### 예시1

이렇게 deleteAll이 있는데 어떻게 개선해야할까?

```java
public void deleteAll() throws SQLException{
	Connection c = null;
	PreparedStatement ps = null;
	ResultSet rs = null;

	try{
		c = dataSource.getConnection();
		ps = c.prepareStatement("select count(*) from users");


		rs = ps.executeQuery();
		rs.next();
		return rs.getInt(1);
	}
	catch(SQLException e) { throw e; }
	finally{
		if(rs != null){
			try{
				rs.close();
			}catch(SQLException e) {}
			...
	}
}

```

전략패턴을 사용하면 아래와 같다.

1. jdbcContextWithStatementStrategy(StatementStrategy stmt) throws SQLException에 ps 쿼리문 빼고 다 넣는다.
    ps의 경우 ps = stmt.makePreparedStatmement(c)로 담는다.
2. 핵심 로직 deleteAll()에는 StatementStrategy st, 오브젝트를 만들어서 jdbcContext~ 파라미터로 넣어준다.
3. AddStatement도 ps 쿼리를 세팅해주고, 파라미터를 세팅해준다. add()에서는 2번처럼 똑같이 해준다.
-> 이렇게 DAO 메소드마다 새로운 StatementStrategy 구현 클래스를 만들어줄 수는 없다.

### 로컬 클래스

로컬 클래스를 사용하면 아래와 같다.

- add()에 class AddStatement를 넣어준다. AddStatement에서 쿼리를 실행하고
    class가 끝나면 StatementStrategy st에 new AddStatement(user);을 한 후 jdbcContextWithStatementStrategy(st)를 해준다.

- addStatement에서 user를 추가해주기 위해 인스턴수 변수 user를 선언해주었는데, 이건 그냥 **로컬 변수**를 사용하면 된다.
    그래서 public void add(final User user) { class AddStatement {...}}로 사용되는데,
    이렇게 **final**을 사용하면 된다.

    그럼 StatementStrategy st = new AddStatement()에서 user를 전달하지 않아도 된다.

### 익명 내부 클래스

AddStatment 클래스는 add() 메소드에서만 사용할 용도로 만들어졌으니, 클래스 이름도 제거 가능

- AddStatement를 익명 내부 클래스로 전환한다.
    StatementStrategy st= new StratementStrategy(){ public Prepared... }로 쿼리 내용을 여기에 넣어서 사용한다.
- 그럼 add(final User user){ jdbcContextWithStatementStrategy(new StatementStrategy(){ 쿼리 내용 })} 이렇게 사용할 수 있다.
- deleteAll도 마찬가지로 바꾸면, public void deleteAll(){ jdbcContextWithStatementStrategy{ new StatementStrategy() {쿼리 내용} } }이 된다.

### JdbcContext의 분리

jdbcContextWithStatementStrategy는 컨텍스트!

### 클래스 분리

비즈니스 로직이 들어있는 클래스 이름은 **JdbcContext**로 설정.  
안에 DataSource 인스턴스가 있고 set으로 DI 받을 수 있게 한다.  
workWithStatementStrategy 함수에 지금까지 봤던 쿼리 실행 try, catch, finally 로직이 들어가있다.

UserDao 클래스는 아래처럼 사용한다.

```java
public class UserDao{
	...
	private JdbcContext jdbcContext;


	public void setJdbcContext(JdbcContext jdbcContext){
		this.jdbcContext = jdbcContext;
	}


	public void add(final User user) throws SQLException{
		new StatementStrategy() { ... }
		);
	}


	public void deleteAll() throws SQLException{
		new StatementStrategy() { ... }
		);
	}
}
```

### DI

의존관계 주입은 인터페이스를 사이에 둬서  
**클래스 레벨에서는 의존관계가 고정되지 않게 하고**  
런타임 시에 **의존할 오브젝트와의 관계를 다이내믹하게 주입해주는 것**이 맞다.  

UserDao와 JdbcContext는 클래스로 의존관계가 설정되어 있어서  
매우 긴밀한 관계로 강하게 결합되어 있다.  
JdbcContext를 스프링 빈으로 등록해서 DI 하는 대신 사용할 수 있는 방법을 알아보자

### DI 의존관계 주입의 3가지 조건

DI! 구체적인 의존 오브젝트와 그것을 사용할 주체, 보통 클라이언트라고 부르는 오브젝트를 런타임 시에 연결해주는 작업

의존관계 주입의 3가지 조건 = ?
1. **인터페이스에만 의존**하기 위해서, 런타임 시점 의존관계가 드러나지 않도록
2. 런타임 시점의 의존관계는 **제3의 존재가 결정**한다
3. 의존관계는 **사용할 오브젝트에 대한 레퍼런스를 외부에서 제공(주입)**해줌으로써 만들어진다!

### 코드를 이용하는 수동 DI

UserDao 내부에서 직접 DI를 적용하는 방법
JdbcContext의 제어권을 UserDao가 갖는 것.

```java
public class UserDao{
	...
	private jdbcContext jdbcContext; // 인스턴스 변수에 저장해두고
	
	// JdbcContext에 대한 생성. DI 작업을 동시에 수행
	public void setDataSource(DataSource dataSource){
		this.jdbcContext = new JdbcContext();

		// 의존 오브젝트 주입(DI)!!
		this.jdbcContext.setDataSource(dataSource);

		// 아직 JdbcContext를 적용하지 않은 메소드를 위해 저장
		this.dataSource = dataSource;
}
```

