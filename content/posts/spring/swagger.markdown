---
title: "Swagger 적용하기"
date: 2021-06-12T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
  - swagger
---


### Swagger 적용하기

1. build.gradle
   <code>
   compileOnly group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2'
   compileOnly group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'
   </code>

2. swagger 파일
  
    class 밖에 @Configuration, @EnableSwagger2
    class 안에 @Bean

    - @Bean
      : @Configuration으로 정의된 클래스는 @Bean으로 정의된 메소드들을 포함

    <script src="https://gist.github.com/h3yon/b619045da35b8c1d066e942dc9477941.js"></script>

    - .consume(), .produces(): 각각 Request Content-Type, Response Content-Type에 대한 설정(선택)
    - .apiinfo(): Swagger API 문서에 대한 설명을 표기하는 메소드(선택)
    - _.apis()_: Swagger API 문서로 만들기 원하는 basePackage 경로**(필수)**
    - _.path()_: URL 경로를 지정하여 해당 URL에 해당하는 요청만 Swagger 문서로 만듦

    <image>https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbWXZuQ%2FbtqA6o6Z7sj%2FxwT3b8YD4bRnyVXO7f4qhK%2Fimg.png</image>
    /member/save
    설명: Member save
    노트: 사용자 저장

    ```java
    @ApiOperation(value = "Member Save", notes = "사용자 저장")
    ```

출처: https://bamdule.tistory.com/36
