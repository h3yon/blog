---
title: "[스프링 핵심 원리 - 기본편] 복습 일지2 (싱글톤)"
date: 2021-10-14T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - TIL
  - spring
---


## [스프링 핵심 원리 - 기본편] 복습일지2 (싱글톤)

### 싱글톤 패턴

클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인패턴!

```java
public class SingleService {
  private static final SingleService instance = new SingleService();
  private SingleService() {}
  public static SingleService getInstance(){ return instance; }
}
```

이 상태로 single.getInstance를 2번 사용했을 때  
같은 객체가 나옴을 알 수 있다.  

근데 싱글톤 패턴의 문제점도 꽤 많다는 걸 알게 되어서  
나중에 한번 더 복습할 때 정리해야겠다.  

### 싱글톤 컨테이너

스프링 컨테이너 하면 ApplicationContext, 빈팩토리가 생각난다.  
근데 싱글톤 컨테이너도 있다고 한다.  

스프링 컨테이너는 알아서 객체 인스턴스를 `싱글톤으로 관리`한다.  
스프링 컨테이너는 싱글톤 컨테이너 역할을 하고,  
싱글톤 객체를 생성하고 관리하는 기능 = `싱글톤 레지스트리`라고 한다.  

스프링 컨테이너가 이래서 `싱글턴 패턴의 모든 단점 해결`한다고 한다.  
싱글톤의 경우 `무상태`로 설계함을 중요시 여기자  
(자세한 내용은 나중에 필요하면 또 정리!)  

###z Configuration과 싱글톤?

Config class에 @Configuration을 넣었었다.  
그럼 스프링이 `CGLIB`이라는 바이트코드 조작 라이브러리로   
다른 클래스를 만들고 그걸 스프링 빈으로 등록한다.  
그래서 싱글톤을 보장한다.


