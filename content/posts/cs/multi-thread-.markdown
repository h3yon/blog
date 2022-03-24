---
title: "멀티스레드"
date: 2021-07-22T23:47:34+09:00
draft: false
categories:
  - cs
tags:
  - cs
---


출처:
[[병렬프로그래밍] 프로세스, 스레드 개념](https://12bme.tistory.com/65?category=682904)
[[병렬프로그래밍] 스레드 이해하기](https://12bme.tistory.com/66?category=682904)

## 스레드 리마인드

프로세스는 실행중인 프로그램이며, 하나의 실행 흐름을 스레드라고 한다.
하나의 프로세스 안에 스레드 여러개 있는 것이 멀티 스레드.

게임/네트워크 프로그래밍에서 멀티 스레드 많이 사용.
(캐릭터를 움직이게 하거나, 데이터를 기다릴 때, 흐름 담당할 때 사용)

## 멀티 프로그래밍

여러 개의 프로그램들이 단일 CPU 상에서 실행되는 것.
한 프로그램이 일부 실행되고 다른 프로그램이 일부 실행되는 방식으로,
모든 프로그램이 동시에 수행되는 것처럼 보이게 된다.

## 자바 스레드의 생성자

```
- Thread() : Thread를 상속받은 고유의 클래스 생성 필요. 클래스 내부에 b()같은 함수 정의
- Thread(Runnable target) : Runnable이 들어간 경우는 인터페이스 구현
- Thread(Runnable target, String name) : Runnable이 들어간 경우는 인터페이스 구현
- Thread(String name)
- Thread(ThreadGroup group, Runnable target)
- Thread(ThreadGroup group, Runnable target, String name)
- Thread(ThreadGroup group, Runnabel target, String name, long stackSize)
- Thread(ThreadGroup group, String name)
```

<img src = "https://user-images.githubusercontent.com/46602874/126585075-77d26485-71fd-403a-9903-de060aceb583.png"/>

자바에서의 스레드 생성 방법은

1. class 상속(white box)
2. interface를 구현(black box)
   2가지 방식이 잇다.

Runnable이 들어간 경우가 2) 인터페이스를 구현하는 경우이며,
Runnable 안에는 Run()함수가 있다.

이 안에 원하는 스레드 로직을 넣으면 된다.

## 런타임 데이터 영역들과 공유 데이터

```
- PC(Program Counter) 레지스터 영역: 스레드 각각 갖고 있으므로 스레드간 독립적인 영역
- 힙영역: 객체 저장되는 부분. 모든 스레드가 접근 가능
- 메소드 영역: 함수영역으로 각 클래스 또는 인터페이스에 런타임 컨텍스트 풀영역, 메소드 생성자를 저장하는 영역으로 모든 스레드에 의해서 공유되는 영역
- runtime context pool 영역: 클래스 또는 인터페이스의 클래스 변수, static 변수, 클래스 객체의 레퍼런스를 저장하는 영역으로 스레드들 간 공유되는 영역
- 데이터와 메소드 스택 영역: 마찬가지로 스레드들 간 공유되는 영역
```

- 공유되는 데이터 영역의 포함 관계  
  객체(힙 영역) > 메소드(메소드 영역) > 변수(런타임 컨텍스트 풀 영역)

## 동기화 in 멀티스레드

: 여러 개의 Thread가 한 개의 자원을 사용하고자 할 때, 해당 Thread만 제외하고 나머지는 접근을 못하도록 막는 것

동기화를 위해서는 공유되는 영역을 보호해줄 수 있는 장치가 필요하다.

임계영역은 공유자원이 참조할 수 있는 코드의 범위이다.
한번에 한 쓰레드만 접근이 가능한 영역으로,멀티스레드 프로그램에서 임계영역을 처리하는 경우 심각한 문제 발생 가능하다.

이 상황을 해결하는 것이 동기화를 이용하는 것이다.
동기화를 처리하기 위해서는 모든 객체에 락을 포함시킨다.
락 = 여러 스레드가 동시에 접근하지 못하도록 하기 위한 것. 모든 객체가 힙 영역에 생성될 때 자동으로 만들어진다.

락은 보통 사용되지 않으며, synchronized 키워드를 사용한다.
synchronized로 묶인 메소드를 보호 구역이라고 할 수 있다.

- 모니터  
  synchronized 키워드를 사용하면 해당 객체의 락을 검사한다. 락의 현재 사용 여부를 검사함으로써 각 객체를 보호한다.

## wait(), notify(), notifyAll()

- wait() 메소드는 어떤 객체에 대해 스레드를 대기하게 한다
- notify() 메소드는 객체에 대해 대기중인 스레드가 있을 경우 우선순위가 높은 스레드 하나만을 깨운다
- notifyAll() 메소드는 대기중인 스레드 전부를 깨운다
