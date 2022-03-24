---
title: "SpringFramework 어노테이션 공부해보기"
date: 2021-06-06T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
---


### Slf4j

: 1. Slf4j: redirect logging

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0f45c0bf-23c9-4771-9b0e-6beba99710f7/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0f45c0bf-23c9-4771-9b0e-6beba99710f7/Untitled.png)

[https://gmlwjd9405.github.io/2019/01/04/logging-with-slf4j.html](https://gmlwjd9405.github.io/2019/01/04/logging-with-slf4j.html)

→ SLF4J Bridging Modules: 다른 로깅 API로의 로거 호출을 SLF4J 인터페이스로 연결(redirect)하여 SLF4J API가 대신 처리할 수 있도록 하는 일종의 어댑터 역할을 하는 라이브러리
→ SLF4J API: **로깅**에 대한 추상 레이어를 제공, 사용자가 이 인터페이스를 통해 logging 코드를 작성
→ SLF4J 특징: 배포시 Logging Framework 선택 가능, 빠른 속도로 작동, 널리 사용되는 Logging Framework를 위한 바인딩 제공(log4j, java.util.logging, 단순 로깅 및 NOP 지원), Bridging legacy logging API(리디렉션하는 여러 Bridging Modules 제공), Migrate your source code(소스 마이그레이션 가능), 매개 변수화된 로그 메시지 지원

→ 퍼사드패턴: 여러 개의 클래스가 하나의 역할을 수행할 때, 대표적인 인터페이스만을 다루는 클래스를 두어 기능을 처리할 수 있게 도와주는 패턴

- 퍼사드에 요청을 전송하여 서브시스템과 통신하여 퍼사드는 해당 요청을 적절한 서브시스템 객체로 전달함
- 퍼사드를 사용하는 클라이언트는 서브시스템 객체에 직접 액세스할 필요가 없다.

### RestController

1. Slf4j: redirect logging

- @Controller 어노테이션과 @ResponseBody 어노테이션을 합쳐놓은 어노테이션,
- 메서드마다 @ResponseBody를 붙여주지 않아도 된다.

### RequiredArgsConstructor

초기화 되지않은 final 필드나, @NonNull 이 붙은 필드에 대해 생성자를 생성

<script src="https://gist.github.com/h3yon/ca42231b6561feb843581b42ba8638f3.js"></script>

- 이 자바 파일을 컴파일 돌리면?
  <script src="https://gist.github.com/h3yon/1f2b5be40c69cb8a10ba21fc37c24897.js"></script>
  → 클래스파일에 생성자가 생성
  → 3개의 리포지토리 = 빈으로 등록이 가능한 존재로 @Autowired 어노테이션 없이 의존성 주입이 이루어짐

### JsonBackReference

    * 순환 참조를 방어하기 위함(자식 클래스에 넣음) - post_id를 함께 가지고 있으니까
    * Member를 조회할 때 Team도 함께 조회 해야 할까?
    * 비즈니스 로직에서 단순히 멤버 로직만 사용하는데 함께 조회하면, 아무리 연관관계가 걸려있다고 해도 손해이다.
    * JPA는 이 문제를 지연로딩 LAZY를 사용해서 프록시로 조회하는 방법으로 해결 한다.

### ManyToOne(fetch = FetchType.LAZY)

: 지연로딩. 실무에서 주로 쓰임
_ 이 말은 회원엔티티를 조회할때 팀회원을 같이 데이터베이스에서 가져오지 않고,
_ MemberFetchTypeLazy.getTeam() 으로 실제로 팀엔티티가 사용될 때
_ 데이터베이스에서 해당 엔티티를 조회해오는 것이다.
_ 출처: https://coding-start.tistory.com/82 [코딩스타트]

### JoinColumn(name = "post_id")

    * @MapsId 는 Many-to-One에서 외래키를 지정할 때 사용
     * @JoinColumn 어노테이션은 외래 키를 매핑할 때 사용하는 어노테이션
     * > name: 매핑할 외래키의 이름을 지정할 때 postpostid(필드명/postid)
     * > unique, nullable, insertable, updatable, table, columnDefinition

### Builder

: getter, setter, 객체 만들어서 name, age(this.name = name;) 이런 식으로 가능
: 상위 User 클래스에서 빌더에도 디폴트 적용하고 싶으면 @Builder.Default

### ManyToOne의 속성

: optional = true: 객체에 null이 들어갈 수 있다.

- @column 어노테이션에서도 nullable=true → Null허용

: fetch - EAGER 관련된 엔티티를 바로 로딩. LAZY를 바로 로딩하지 않고 객체 조회할 때 엔티티 로딩
: cascade: 영속성 전이 설정을 할 수 있다.
: targetEntity - 연관된 엔터티의 타입 저옵를 설정하는데 거의 사용하지 않는다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f12366aa-2f70-43a7-9b78-11e29992c939/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f12366aa-2f70-43a7-9b78-11e29992c939/Untitled.png)
