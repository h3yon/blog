---
title: "[TIL]스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술"
date: 2021-10-03T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - TIL
  - spring
---

### [TIL] 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술

(주의) 주관적으로 정리하고 싶은 부분만 정리했습니다!  
 요약본을 보고 싶으신 분은 다른 게시글을 보시는 걸 추천합니다~!

본 강의:
[인프런 - 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard)

1. 객체를 계속 생성하는 것보다는  
   의존성 주입으로 재활용할 부분은 재활용하기

   ```java
   private final MemberRepository memberRepository;

   public MemberService(MemberRepository memberRepository) {
         this.memberRepository = memberRepository;
     }
   ```

2. 스프링 컨테이너

   스프링이 관리하게 되면  
   스프링 컨테이너에 등록하고 받아서 쓰도록 관리  
   new해서 사용하면 다른 컨트롤러로 하면  
   다른 컨트롤러에서 가져다 쓸 때 객체를 new.

   하나로 하고 재활용해서 쓰면 됨.

3. Autowired

   스프링 컨테이너가 뜰 때 컨트롤러 생성함.  
   그럼 아래 생성자 호출.  
   Autowired가 붙어있으면 멤버 서비스를 가져다가 연결시켜줌  
   -> 의존관계 주입

   ```java
       @Autowired
       public MemberController(MemberService memberService) {
           this.memberService = memberService;
       }
   ```

4. 스프링 빈을 등록하는 2가지 방법

   - 컴포넌트 스캔과 자동 의존관계 설정

     - 컴포넌트 스캔!  
       `@Component` 또는 `@Controller/Service/Repository`로 객체를 생성해서 스프링 컨테이너에 등록함  
       위의 어노테이션을 통해 `스프링 빈`으로 자동 등록
     - Autowired  
       컨트롤러, 서비스, 레퍼지토리 연관관계를 설정해줘서 서로 쓸 수 있게 함.

     > 참고: 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 싱글톤으로 등록.(유일하게 하나만 등록해서 공유)  
     > 따라서 스프링 빈이면 모두 같은 인스턴스!

   - 자바 코드로 직접 스프링 빈 등록

     정형화되지 않거나, `상황에 따라 구현 클래스를 변경`해야될 경우 사용  
      ex) 레퍼지토리를 다른 레퍼지토리로 바꿀건데,  
      기존의 운영코드를 !하나도! 손대지 않고 바꿔치기 할 때

   ```java
   @Configuration
   public class SpringConfig {

       @Bean
       public MemberService memberService(){
           return new MemberService(memberRepository());
       }

       @Bean
       public MemberRepository memberRepository(){
           // 구현체 리턴
           return new MemoryMemberRepository();
       }
   }
   ```

5. 의존성 주입(DI) 3가지 방법

   아래 3가지 예시로 살펴보자.  
   참고로 셋다 같은 의미를 가진다.

   - 필드 주입

     간결한데, 중간에 바꿀 방법이 없다.

     ```java
       public class MemberService {

         @Autowired
         private final MemberRepository memberRepository;
       }
     ```

   - setter 주입

     public으로 되어있고,  
     중간에 잘못 바꾸면 문제될 수 O

     ```java
       public class MemberService {

         private MemberRepository memberRepository;

         @Autowired
         public void setMemberService(MemberService memberService){
           this.memberService = memberService;
         }
       }
     ```

   - 생성자 주입(권장됨!!)

     의존관계가 동적으로 변하는 경우가 거의 없어서 권장됨

     ```java
     public class MemberService {

       private final MemberRepository memberRepository;

       public MemberService(MemberRepository memberRepository) {
             this.memberRepository = memberRepository;
         }
     }
     ```

   > 주의: `Autowired`를 통한 DI는 스프링이 관리하는 객체에서만 관리된다.
   > 스프링 빈으롣 등록하지 않고 내가 직접 생성한 객체에서는 동작되지 않는다.

6. 개방-폐쇄 법칙(OCP, Open-Closed Principle)

   확장에는 열려있고, 수정/변경에는 닫혀있다.

7. AOP

   ```java
   @Aspect // AOP!
   @Component //config에 bean해도 되고 Component로 해도 되고
   public class TimeTraceAop {

       @Around("execution(* hello.hellospring..*(..))")
       public Object execute(ProceedingJoinPoint joinPoint) throws Throwable{
           long start = System.currentTimeMillis();
           try {
               return joinPoint.proceed();
           } finally {
               long finish = System.currentTimeMillis();
               long timeMs = finish - start;
               System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
           }
       }
   }
   ```
