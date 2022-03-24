---
title: "[TIL] Spring 심화반 2주차 복습일지"

toc: true
toc_sticky: true

categories:
  - Java
tags:
  - Java
---

## [TIL] Spring 심화반 2주차 복습일지

2주차때 배운 내용을 복습해보려고 한다~!  
간단한 목차는 아래와 같다!  

<img src="https://user-images.githubusercontent.com/46602874/135745966-83843040-7929-4c2e-a179-85de4b5464f9.png">

## * 인증과 인가!

인증은 신원 확인!  
인가는 사용자 권한을 확인한다는 의미  


## * 스프링 시큐리티?

인증 및 인가를 위해 많은 기능 제공!  

```java
implementation 'org.springframework.boot:spring-boot-starter-security'
```

## ** 스프링 시큐리티 과정 살펴보기

아래처럼 UserDetails 관련해서 뭔가 작용하는 구조임을 알 수 있다!  

<img src="https://user-images.githubusercontent.com/46602874/135748176-b70c0e20-c4e2-4b9c-aebe-9434ce0a81de.jpeg">

아래 2개 구현을 해준다.  

1. `UserDetailsService` 인터페이스 → `UserDetailsServiceImpl` 클래스

    UserDetailsServiceImpl에서는 해당하는 유저 이름을 리턴해주도록 구현한다.

2. `UserDetails` 인터페이스 → `UserDetailsImpl` 클래스

    UserDetailsImpl에서는 User를 가지고 있도록 한다.


스프링 시큐리티를 사용했기 때문에  
`@AuthenticationPrincipal` 어노테이션을 통해 User 정보가 들어옴을 알 수 있다!

```java
public Product createProduct(@RequestBody ProductRequestDto requestDto, @AuthenticationPrincipal UserDetailsImpl userDetails) {
        // 로그인 되어 있는 ID
        Long userId = userDetails.getUser().getId();
```

참고로, `AuthenticationManager`은  
OAuth2 때 config 파일에 추가해준다.  
(이 부분 더 복습하기!!)  


## * 회원가입에서 enum

아래처럼 일단 default role을 잡아놓고,
검증한 후 맞으면 맞는 role을 설정해준다

```java
Optional<User> found = userRepository.findByUsername(username);
if (found.isPresent()) {
    throw new 에러;
}

// 사용자 ROLE 확인
UserRole role = UserRole.USER;
if (requestDto.isAdmin()) {
    if (!검증검증) {
        throw new 에러;
    }
    role = UserRole.ADMIN;
}
```

## * 비밀번호 암호화

OOP가 정말 중요하다고 한번 더 느낀 게,  
나는 비밀번호를 암호화한다고 하면 Service에서 길게 처리할 거 같았다  
근데 Config에 Bean을 주입시켜서 처리를 하는 걸 보고,  
OOP의 중요성을 한번 더 깨달았다!


## ** 인가 과정!

스프링 시큐리티가 로그인한 회원의 권한을 인식하도록 UserDetailsImpl을 수정한 부분이 있는데, 그 부분이 진짜 어려운 것 같다.   
SimpleGrantedAuthority 클래스를 사용해서 권한을 인식한다.    

config 파일에서는 `@EnableGlobalMethodSecurity(securedEnabled = true)`를 추가해준다.  
또, `@EnableWebSecurity`를 추가해준다.  

그래서 컨트롤러에서 인가가 필요한 API에   
`@Secured("{ROLDE 이름}")`을 추가해준다.


## ** 그 외 다룬 부분

  - OAuth2로 카카오 로그인
      카카오 로그인을 하고, 로그인 처리 과정 부분이 어려웠다.  
      (카카오 로그인은 구현해본 경험 있으니 패스!)  

      스프링 시큐리티를 사용하기 때문에  
      `UsernamePasswordAuthenticationToken`와 `authenticationManager`가 사용된다.  
      `SecurityContextHolder`에서 getContext를 하고 authentication을 set해주는데,  
      그 부분을 한번 더 봐야될 것 같다ㅎㅎ

## 소감!!

이번 2주차가 진짜 어려웠던 것 같다.  
정말 공부할 부분은 끊임 없는 것 같다ㅎㅎ  

카카오 로그인은 좀 구현해본 적이 있어서   
쉽게 쉽게 생각했는데 다시 복습해보니 어려운 부분이 수두룩하다  

한번 더 복습해봐야겠다!