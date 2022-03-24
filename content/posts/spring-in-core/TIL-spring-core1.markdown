---
title: "[스프링 핵심 원리 - 기본편] 복습 일지1 (빈)"
date: 2021-10-13T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - TIL
  - spring
---


## [스프링 핵심 원리 - 기본편] 복습일지1 (빈)


원래 설정 파일 즉, AppConfig에서   
memberService -> memberRepository -> MemoryMemberRepository   
이런 식으로 사용했었다.  

근데 AppConfig를 @Configuration으로 해주고,  
각각 memberService, memberRepository, MemoryMemberRepository에  
@Bean으로 등록해주면 어떻게 되는지 보자.

### @Configuration, @Bean으로 등록했을 때

원래 기존 방식은 아래와 같다.

```java
AppConfig appConfig = new AppConfig();
MemberService memberService = appConfig.memberService();
```

@Configuration, @Bean으로 등록해주면  
위의 부분이 어떻게 바뀌는지 보자.  

```java
ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
MemberService memberService = ac.getBean("memberService", MemberService.class);
```

이렇게 ApplicationContext = `스프링 컨테이너` 임을 알 수 있다.  
AppConfig에서 직접 객체를 생성하고 DI 하는 방식에서  
@Bean으로 등록된 메서드를 모두 호출해서 반환된 객체`스프링빈`를  
`스프링 컨테이너`에 등록함을 알 수 있다.  

등록된 부분을 getBean으로 사용함을 알 수 있다.  
어노테이션 기반이기 때문에 AnnotationConfigApplicationContext를 썼지만,  
XML 기반이면 GenericXmlApplicationContext을 써도 된다.  

### Bean 출력

등록된 bean을 출력하는 메서드들에 대해서 알아보자.  

```
// 모든 빈 배열
String[] beanDefinitionNames = ac.getBeanDefinitionNames();

// 직접 등록한 빈 출력
BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

// ROLE_APPLICATION / ROLE_INFRASTRUCTURE?
// : 직접 등록 / 스프링이 사용
BeanDefinition.ROLE_APPLICATION

// 빈 이름 / 타입 / 구체 타입으로 조회 메서드
ac.getBean();

// 해당 타입의 모든 빈 조회
Map<String, Policy> beansOfType = ac.getBeansOfType(Policy.class);
```

스프링 빈에 대해서 타입으로 조회할 때,  
중복일 경우 에러가 난다.  
그러면 이름으로 조회하면 된다.  
(getBean의 사용 예시는 검색하면 많이 나올 것 같아서 패스~!)    

부모 타입으로 조회시 자식이 둘 이상 있어도 에러가 난다.  
유사하게 자식이 둘 이상 있으면, 빈 이름을 지정하면 된다.  

### 빈팩토리와 ApplicationContext

BeanFactory가 최상위에 있고, 빈을 관리/조회 역할을 담당함에도   
ApplicationContext를 사용해왔다.  
ApplicationContext가 빈팩토리 기능을 모두 상속 받아서 제공하고,  
수많은 부가기능을 제공하기 때문이다.  
(그 부가 기능은 나중에 중요성을 한번 더 느꼈을 때 정리할 것 같다!)  
+빈팩토리 또는 ApplicationContext = 스프링 컨테이너

### BeanDefinition

Annotation, XML 기반 ApplicationContext를 다룰 때,  
각각 자바 코드/XML을 읽어서 BeanDefinition을 만든다.  
이 설정 메타정보`BeanDefinition`을 기반으로 스프링 빈을 생성한다.  
BeanDefinition에 대해서도 더 중요성을 느끼면 한번 더 정리해야겠다!  
(아직 나에겐 너무 어렵다ㅎㅎ)  


### 핵심 요약

1. 역할과 구현을 분리하자
2. ApplicationContext -> 스프링 컨테이너
3. 객체 지향의 원리
4. Bean!