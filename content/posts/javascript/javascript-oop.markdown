---
title: "Javascript 객체지향"
date: 2021-06-06T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - oop
  - javascript
---


### Javascript 배우기

Javascript는 웹 브라우저에서 실행된다.(클라이언트 측)
JSP, Servlet, ASP는 웹서버에서 해석되고 실행되어 결과만 HTML로 변환되어 웹 브라우저에 보여준다.(서버측)
웹서버에서 실행되기 때문에 즉각적인 반응을 하지 못하고 시간이 오래 걸린다.

### 프로토타입 기반 객체 지향

자바스크립트는 프로토타입 기반 객체지향 언어로,
자바처럼 클래스로부터 내용을 상속 받아서 객체 만드는 것이 아니라,
그냥 간단하게 객체를 생성하고, 속성과 값을 추가, 변경, 삭제 가능하다.

```javascript
var person = { name: "h3yon", age: 20 };
console.log(person);
// Object
//  age: 20
//  name: "h3yon"
//  __proto__: Object
```

#### eval

```javascript
eval("var a = 1, b = 2;");
console.log(a, b); // 1 2
```

eval()을 통해 프로그램 실행 도중에도 동적으로 코드 실행이 가능하다.

#### 고차 함수

간단하게 파라미터가 함수이거나,
함수를 반환할 수 있는 함수이다.

```javascript
function (param_func) => {
   param_func();
   return function test(){
      ...
   }
}
```

#### 자바스크립트 엔진

자바스크립트 엔진은 개발자가 작성한 js 코드를 해석하고 실행시켜주는 것이다.
목표는 가능한 짧은 시간 내에 가장 최적화된 코드를 생성하는 것이다.

자바스크립트 엔진은

> 코드를 실행하는 프로그램 혹은 인터프리터

자바스크립트 엔진의 종류는 아래와 같다.

```
V8: 구글에서 개발한 오픈소스. C++로 작성, 구글 크롬과 Node.js에서 사용(가장 유명)
SpiderMonkey: 최초의 자바스크립트 엔진
Rhino: 모질라재단의 오픈소스. 자바로 구현.
JavaScriptCore: 애플이 사파리를 위해 개발
Chakra: 인터넷 익스플로러/마이크로소프트 엣지용
Nashron: 오픈 JDK의 일환으로 오픈소스. Oracle ~이 개발
JerryScript: 사물인터넷 위한 엔진
```

#### 익명 함수

익명 함수는 함수가 객체로서 취급되고 처리되기 때문에 가능하다.
'익명'처럼 특별히 이름을 안 지어주어도 된다.

```javascript
var test = function (a) => {
   return "익명 함수";
};
console.log(test());
```

보통 함수는 function 함수이름(파라미터){ ... } 형식으로 사용되는데,
익명 함수는 function (파라미터) => { ... } 형식으로 사용된다.

#### 콜백 함수

특정 이벤트가 발생하면 호출되는 함수이다.
예를 들어 Promise에서 성공 또는 실패 시 콜백함수가 사용될 때가 있다.

```javascript
function one() {
  return 1;
}
function invoke_and_add(a, b) {
  return a() + b();
}
invoke_and_add(one, function () {
  return 2;
});

//3
```

위의 예제는 고차 함수가 사용된다.
파라미터에 함수가 들어감으로써 function() { return 2; }가 들어가, 2를 말함을 뜻한다.
그래서 invoke_and_add(one, 2);를 말하는 것이고,
one() + 2 = 3이 나옴을 알 수 있다.

#### 객체 지향

객체는 그냥 간단하게 생각하면, 사람이 있다고 가정하자.
사람마다 경제상황, 소유물, 성격 모두 다른 부분을 가진다.
사람 a, 사람 b, ... 이런 부분을 객체라고 생각하면 될 것 같다.

> 객체 지향의 요소를 알아보자

- 클래스
  : 일종의 틀. 템플릿. 공통점을 묶은 집합체라고 생각해도 될 것 같다.

  ```javascript
  var Person = function () {};
  Person.prototype = {
    name: String,
    age: Number,
    school: String,
  };

  var h3yon = new Person();
  h3yon.name = "H3yon";
  h3yon.age = "20";
  ```

  이런 식으로 사용될 수 있다.

  자바스크립트에서 신기한 것은, 모든 것들이 객체에 기반을 두고 있다.
  In 자바스크립트, 원형(프로토타입)이라는 표기법이 있다.
  이를 이용하여 객체가 생성된다.

- 캡슐화부터 다음에!

출처: [한 눈에 끝내는 nodejs](https://edu.goorm.io/learn/lecture/557/%ED%95%9C-%EB%88%88%EC%97%90-%EB%81%9D%EB%82%B4%EB%8A%94-node-js)
