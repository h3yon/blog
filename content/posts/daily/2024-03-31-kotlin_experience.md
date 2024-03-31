---
title: "코틀린 사용했던 것에 대하여"
date: 2024-03-31T23:47:34+09:00
draft: false
categories:
  - diary
tags:
  - diary
  - kotlin
---

# 코틀린?

JetBrains사에서 개발하는 크로스 플랫폼 범용 프로그래밍 언어로 `자바와 완벽하게 호환`되는 점이 큰 장점이다.  
`JVM` 기반의 언어이며 JVM 바이트코드가 기본이다.  
하지만 원한다면 Kotlin, Native 컴파일러를 사용할 수 있고 안드로이드, 스프링 Framework, tomcat, Javascript,  
HTML5, iOS, 라즈베리 파이 등을 개발할 때 사용할 수 있다.  
이렇듯 많은 곳에서 사용되기 때문에 부담이 적은 서비스에서는 코틀린을 많이 택하지 않을까 싶기도 하다.(개인적인 의견)    
  
# 왜 코틀린을 사용할까?

자바와 완벽하게 호환되고, 같은 JVM 기반의 언어라면 그냥 자바를 쓰면 되지 않을까? 라고 생각할 수 있다.  
심지어 자바 17부터는 toList를 그냥 사용할 수 있고,  
코틀린에서 사용되는 data class처럼 record 사용, switch 등 코틀린처럼 간결해지는 걸 지향하고 있는 듯 하다.  

자바가 점점 간결해짐에도 보일러플레이트 코드 발생이 언급된다.  
stream 으로 만들고 toList()를 해준다거나(개인적 의견)  
null check, getter, setter 등등이 그 예시다.  

# 장점 - 예제 코드를 통해 확인해보자.

1. Getter, Setter, 생성자 (자바의 record와 동일)  
    JAVA
    ```java
    public class MyDTO {
        private final int data;
    
        public MyDTO(int data) {
            this.data = data;
        }
    
        public int getData() {
            return data;
        }
    }
    ```
    KOTLIN
    ```kotlin
    data class MyDTO(var data: int)
    ```
2. NPE를 걱정하지 않아도 된다. 컴파일 타임에 에러를 발생시켜준다.  
    코틀린에서 MyDTO? 가 아닌 MyDTO 만 있으면 null일 수가 없다는 것. 자바의 primitive type  
      
    JAVA
    ```java
    public String getName(MyDTO myDTO) {
        if(myDTO != null){
            return myDTO.getName();
        }
        return "";
    }
    ```
    KOTLIN
    ```kotlin
    fun getName(myDTO: MyDTO?): String {
        return myDTO?.name ?: ""
    }
    ```
3. stream  
    JAVA 17
    ```java
    public List<String> getNames(List<String> names) {
        return names.stream()
                     .map(name -> "name: " + name)
                     .toList();
    }
    ```
    KOTLIN
    ```kotlin
    fun getNames(names: List<String>): List<String> = names.map { "name: " + it }
    ```
4. val
    ```
    // JAVA
    private final String name = "h3yon";
    // KOTLIN
    val name = "h3yon"; // 조작 시 exception

    // JAVA
    private String name = "변경가능";
    // KOTLIN
    var name = "변경가능";
    ```
5. 확장함수  
    기존에 있던 거에 원하는 기능 확장 가능
    ```kotlin
    fun String?.appendNameAndGet(): String = "name" + this

    var h3yonName = "h3yon"
    var test = h3yon.appendNameAndGet() // name: h3yon
    ```
6. 구조분해
    ```kotlin
    fun main() {
      val (name, extension) = splitFilename("helloWorld.kt")
      println("$name.$extension")
    }
    ``` 
그 외 다양한 장점이 있다.

# Kotlin 단점?

단점은 뭐가 있을까?   
위에 장점만 나열해서 편안해보고 간단해보일 수 있지만,,, 학습 리소스가 들 수밖에 없다  
개인적으로 코틀린의 scope function도 익숙해지는 데 시간이 필요했다.  
  
자바가 더 빠르다.  
코틀린은 컴파일 시 자바로 변환하고 바이트 코드로 변환하기 때문이다.  
