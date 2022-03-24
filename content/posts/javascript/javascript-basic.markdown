---
title: "Javascript 기초지식 쌓기"
date: 2021-07-18T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - javascript
---



출처: https://velog.io/@dongha1992/javascript-%EB%82%B4%EB%B6%80%ED%95%A8%EC%88%98-%EC%99%B8%EB%B6%80%ED%95%A8%EC%88%98

### 자바스크립트 기초 지식 쌓기

아래의 코드가 있다고 가정하자.

<!-- <script src="https://gist.github.com/h3yon/341bb316d062f173230a08f2b47127d3.js"></script> -->

```javascript
function out(){
  const name = "aaa"
  return function(){
    console.log(name);
  }
}

const in = out()
in()
```

- 함수 안에 함수가 있을 때 응집성이 높아진다.
- 위의 코드를 보면 out()은 외부함수, in()은 내부함수를 말한다.
- in()함수에서 name에 대한 로그를 찍는데, name은 out()에만 있기 대문에 외부 함수에서 변수를 찾는다. 이를 closure라고 한다.

### Closure 클로저

내부 함수에서 외부 함수의 지역 변수에 접근하는 것

<script src="https://gist.github.com/h3yon/c993937ca549b5423ff36776d5b1c313.js"></script>

위의 코드를 보면 외부함수 안에 지역변수가 있고,
return 값은 내부함수임을 알 수 있다.
내부 함수 안에서 console.log가 나온다.

in은 out()의 리턴값이다.
즉 아래 소스코드와 같다.

<script src="https://gist.github.com/h3yon/de6914832a3f7a0386d142c357fd1f50.js"></script>

함수를 호출하면 'aaa'가 출력된다.
여기서 out()은 내부함수를 return하는 동시에 종료됐다.
하지만 in()을 호출하면 out(), 외부 함수에 존재하는 지역 변수를 가져왔다.
외부함수가 종료된 이후에도 내부함수를 통해 변수에 접근할 수 있는 것이 클로져의 특성이다.

### 지역 변수

<script src="https://gist.github.com/h3yon/cd2046f7898a2a4ffb0c366076199ee5.js"></script>

return 값 = 객체. 객체는 get_food, set_food라는 키(메서드)를 가지고 있고, 이 메서드들은 내부함수이다.

get_food의 value는 food를 리턴하는 함수이다.
get_food 메서드를 호출하면 지역변수에 접근할 수 있다.
set_food의 \_food는 내부 변수를 지칭한다.
meal 함수는 return하는 동시에 종료되어, meal의 지역변수는 내부 함수를 통해서만 접근 가능하다.

### 클로져 응용

var로 변수를 선언했을 때와 let으로 선언했을 때 출력값이 다르다.

- var로 선언했을 때

<script src="https://gist.github.com/h3yon/3e3fe0f3d5f893a05e06f849e78d085a.js"></script>

처음 위의 코드를 봤을 때,

- console.log(arr[index]()) ;
  의 결과값은 0 1 2 3 4 일 것이라고 생각했다.
