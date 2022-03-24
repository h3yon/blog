---
title: "[스프링 핵심 원리 - 기본편] 복습일지3 (컴포넌트 스캔)"
date: 2021-10-15T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - TIL
  - spring
---

# [스프링 핵심 원리 - 기본편] 복습일지3 (컴포넌트 스캔)

## 컴포넌트 스캔?

컴포넌트 스캔은 스프링이 설정 정보가 없어도  
`자동으로 스프링 빈을 등록`하는 기능이다  

컴포넌트 스캔을 사용하려면 아래처럼  
`@ComponentScan`을 설정 정보에 붙여준다.  
아래처럼 제외도 가능하다.

```java
@Configuration
@ComponentScan(
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {
}
```

컴포넌트 스캔은 `@Component` 애노테이션이 붙은 클래스를 스캔 후  
스프링 빈으로 등록한다.  

예시를 한번 봐보자.

```java
// 1. DetailPolicy.class
@Component
public class DetailPolicyRepository implements PolicyRepository{}

// 2. PolicyServiceImpl.class
@Component
public class PolicyServiceImpl implements PolicyService{

  private final PolicyRepository policyRepository;

  @Autowired
  public PolicyServiceImpl(PolicyRepository policyRepository){
    this.policyRepository = policyRepository;
  }
}
```

`Bean`으로 설정 정보 작성, 의존 관계도 명시했었는데,  
이런 설정 정보가 없어서 이 클래스 안에서 DI를 해결해야 한다.  
`@Autowired`로 의존관계를 자동 주입해준다.  

이 부분을 사용할 때엔,  
Bean을 사용했을 때처럼 ApplicationContext, getBean을 사용하면 된다.  

## 컴포넌트 스캔의 동작

`ComponentScan`은 `Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다.  
빈 이름은 클래스 맨앞글자가 소문자로 바뀐 걸로 지정된다.  
만약 직접 지정하고 싶으면 @Component("원하는 이름")으로 진행하면 된다.  

그렇다면 `Autowired`는 어떤 동작을 할까?  
생성자에 Autowired를 지정하면,
스프링 컨테이너가 자동으로 `스프링 빈을 찾아서 주입`한다.  

## 컴포넌트 스캔 탐색 위치

컴포넌트 스캔은 컴포넌트가 붙은 모든 클래스를 스프링 빈으로 등록한다고 했는데,  
다 스캔하면 시간이 오래 걸려서, 꼭 필요한 위치부터 탐색한다.

```java
@ComponentScan(
          basePackages = "패키지명.원하는_위치",
}
```

이렇게 원하는 탐색 시작 위치를 지정할 수 있다.

## 컴포넌트 스캔 기본 대상

컴포넌트를 찾아서 등록하기도 하지만,  
`@Controller`, `@Service`, `@Repository`, `@Configurtation`도  
대상에 포함된다.  

스캔 대상을 추가/제외도 가능하다.  
그럴 땐 ComponentScan어노테이션에  
`includeFilters`, `excludeFilters`를 사용하면 된다.  

## 나만의 컴포넌트 대상 어노테이션 만들기

```java
@Target(ElementType.TYPE) // class
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {
}

// 사용할 때 다른 클래스에서의 ComponentScan
@ComponentScan(
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
  )
```

필터 타입의 경우,  
어노테이션 외에도 정규표현식, CUSTOM 등이 있다.  

