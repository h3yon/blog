---
title: "[TIL] Spring 심화반 1주차 복습일지"

toc: true
toc_sticky: true

categories:
  - Java
tags:
  - Java
---

## [TIL] Spring 심화반 1주차 복습일지

1주차때 배운 내용을 복습해보려고 한다~!
간단한 목차는 아래와 같다!

<img src="https://user-images.githubusercontent.com/46602874/135743751-a179239f-1fa6-42dd-81a2-dae0e3d15526.png">


### 세팅 관련

- preference에서 auto import 입력
  - Insert imports on paste: Always
  - Add unambiguous imports on the fly: v로 바꾸기

### 의존성 주입

생성자 주입 방식으로 의존성 주입하는 부분임을 알 수 있다.

```java
@Controller
public class MyController{

  private final MyService myService;

  @Autowired
  public MyController(MyService myService){
    this.myService = myService;
  }
}
```

### 생성자 직접 생성

entity에서는 AllArgsConstructor를 사용 X, 아래처럼 진행  
DTO에서는 AllArgsConstructor를 사용 O 

```java
public Product(ProductRequestDto requestDto) {
    this.title = requestDto.getTitle();
    this.image = requestDto.getImage();
    ...
}
```

### Timestamped 클래스

Timestamped 클래스에는  
@MappedSuperclass, @EntityListeners(AuditingEntityListener.class)  
어노테이션이 사용되고 추상 클래스가 사용된다.  

SpringcoreApplication에는   
@EnableJpaAuditing를 추가해주어야 한다.  

### 요청 처리

아래와 같은 구조를 가짐을 알 수 있다.  

<img src="https://user-images.githubusercontent.com/46602874/135742638-94f35011-489e-4e10-a669-f4d2275ea90d.jpeg">

### DI, IoC

  - DI(Dependency Injection) 이미 생성된 객체를 가져오는 작업
  - IoC(Inversion of Control) 보통 자신이 필요한 객체를 생성해서 
    사용하는 것이 일반적인데, 필요한 객체 요청하면 쥐어지는 것

### 빈, 컨테이너

  - 빈: 스프링이 생성해주는 객체
  - 스프링 IoC 컨테이너: 빈을 모아둔 통

### 그 외 배운 부분

    - 역할 분리
    - query