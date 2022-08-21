---
title: "상속보다는 컴포지션(조합)"
date: 2022-08-02T03:47:34+09:00
draft: false
categories:
  - diary
tags:
  - diary
  - 조합
  - 상속
---


## 상속보다는 컴포지션(조합) Background

코드를 짜면서 상속으로 중복 코드도 줄일 수 있고,  
더 효율적으로 생각되었다.  
근데 언제부턴가 상속보다는 조합을 사용하란 말이 많아졌다.  

예시를 보자.

```java
public class PurchasedProduct {
    protected List<Integer> productNo;
    ...
}
```

위의 클래스를 상속 받는 클래스가 있다고 가정하자.

```java
public class Purchased extends PurchasedProduct {
     private List<Integer> dealNo;
     ...
}
```

그런데 상위 `PurchasedProduct` 에서 List 대신 `int[] productNo` 로 바뀌었다고 가정하면  
`PurchasedDeal` 클래스도 바꿔주어야 할 것이다.

## 컴포지션(조합)?

기존 클래스가 새로운 클래스의 구성 요소로 쓰인다는 뜻에서 이러한 설계를 `컴포지션`이라고 한다.

```java
public class Purchased {
    private PurchasedProduct purchasedProduct;
    private List<Integer> dealNo;
}
```

그럼 상위에 있던 `purchasedProduct` 클래스의 내용이 변화되어도 영향이 적고, 안전하다.

## 정리

상속은 좋으나 캡슐화 원칙을 침해하므로 문제를 발생시킬 수 있다.
- is-a 관계가 있을 때만 사용하자
- 관계가 되어도 다른 패키지에 있거나 계승을 고려하지 않은 상위 클래스라면 하위 클래스는 깨지기 쉽다

이럴 때에는 조합이 낫다.




