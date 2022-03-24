---
title: "Javascript 변수 var, let, const & TDZ(Temporal Dead Zone)"
date: 2021-07-20T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - variable
  - javascript
---


### 출처

[자바스크립트 변수 선언 방식 차이: var/let/const](https://curryyou.tistory.com/192)
[TDZ(Temporal Dead Zone)이란?](https://noogoonaa.tistory.com/78)
[Hoisting and TDZ](https://velog.io/@open_h/Hoisting-and-TDZ)

### Javascript 변수 var, let, const & TDZ(Temporal Dead Zone)

오늘은 기초적인 var, let, const에 대한 차이를 알아보았다
일단 변수 선언의 3단계는 선언 -> 초기화 -> 할당 과정으로 이루어져 있다.

> 선언: 변수를 실행 context의 변수 객체에 등록하는 단계. 해당 변수 객체는 scope가 참조하는 대상이 됨 \n
> 초기화: 선언 단계의 변수를 위한 메모리를 만드는 단계. 할당된 메모리에는 undefined로 초기화 \n
> 할당: undefined로 초기화된 메모리의 다른 값을 할당 \n

1. var
   일단 var을 봐보자.

   ```javascript
   var time;
   console.log(time); // undefined
   time;
   ```

   var은 선언과 초기화 단계가 동시에 진행된다.
   때문에 저렇게 값을 할당하지 않아도 undefined로 호출되는 호이스팅이 발생한다.

2. let
   그다음, let을 봐보자.
   let은 var과 다르게 선언단계와 초기화 단계와 분리되어 진행된다.

   ```javascript
   let time; //선언하여 실행 context에 변수를 등록했다.
   console.log(time); // error
   time;
   ```

   선언 단계가 진행되었지만
   초기화, 즉 메모리가 할당되지 않아 접근할 수 없어 참조 에러가 발생한다.

### var vs let

1. 중복 선언

   var은 아래 소스코드처럼 중복 선언이 가능하다.

   ```javascript
   var num = 1; //(선언 + 초기화)
   console.log(num); //1
   var num = 2; //(선언 + 초기화)
   console.log(num); //2
   ```

   const와 let은 중복 선언이 불가능하다.

   ```javascript
   let num = 1;
   let num = 2; // SyntaxError: 이미 declared

   const num1 = 1;
   const num1 = 2; // SyntaxError: 이미 declared
   ```

2. 재할당

   ```javascript
   var num = 1;
   num = 2;
   console.log(num); //2

   let num1 = 1;
   num1 = 2;
   console.log(num1); //2
   ```

   둘다 선언 및 초기화 이후 다른 값을 재할당할 수 있다.
   const는 재할당이 불가능하다.
   (const는 처음 선언 시 반드시 초기화 필요)

3. scope

   scope? 유효한 참조 범위.

   var은 함수 내부에 선언된 변수만 지역변수로 한정하며, 나머지는 모두 전역변수로 간주한다.
   let과 const는 함수 내부는 물론, if문이나 for문 등의 코드 블럭에서 선언된 변수도 지역변수로 취급한다.

   그래서 var은 '함수 레벨 스코프',
   let과 const는 블록 내부 변수도 지역변수로 사용하므로 '블록 레벨 스코프'이다.
   블록: {}로 둘러싸인 코드 영역

4. 호이스팅

   자바스크립트는 코드를 실행하기 전, 일종의 '코드 평가 과정'을 거치는데,
   '변수 선언문'을 미리 실행해두기 때문에 뒤에서 선언된 변수도 앞의 코드에서 참조할 수 있게 된다.
   이를 변수 호이스팅이라고 한다.

   ```javascript
   console.log(num2); // undefined
   var num2 = 1;
   console.log(num2); // 1

   console.log(num); // ReferenceError: 정의되지 않음
   let num = 1;
   console.log(num);
   ```

   위에 var, let을 보자.
   var 변수의 호이스팅은 코드 실행 전에 미리 변수를 선언하고, undefined로 초기화해둔다.
   let/const 변수의 호이스팅은 코드 실행 전에 변수 선언만 진행해두며, 초기화는 실행 과정에서 만났을 때 수행한다.
   그래서 let/const는 호이스팅이 발생하기는 하지만, 값을 참조할 수 없어서 호이스팅이 발생하지 않는다.

   <img src="https://user-images.githubusercontent.com/46602874/127586681-81107bdb-b4da-43b5-bc32-dc913e61db28.png">

   위의 예시 코드를 보면 알겠지만,
   왼쪽의 코드가 오른쪽처럼 선언만 끌어올려지는 것을 알 수 있다.

   <img src="https://user-images.githubusercontent.com/46602874/127588033-80a12f0e-3687-49f8-8013-734c70fa2998.png">

   이번엔 함수 호이스팅을 봐보자
   h2()함수는 변수만 호이스팅이 되기 때문에 에러가 발생하면서 프로그램이 종료된다.
   다른 부분은 똑같이 undefined가 나옴을 알 수 있다.

   <img src="https://user-images.githubusercontent.com/46602874/127588586-5ec5dcbd-9027-4549-bd2f-84e4a5e922a0.png">

   위 예시까지는 당연하다고 생각될 수 있는데,
   만약 함수에서 초기화가 같이 진행되면 어떨까?

   ```
   undefined
   5
   undefined
   ```

   결과는 위와 같다.
   f1 함수는 그자체가 위로 호이스팅되기 때문에 제대로 된 값 5가 나온다.
   f2는 식별자가 호이스팅되기 때문에 undefined로 나옴을 알 수 있다.

### TDZ(Temporal Dead Zone)

TDZ는 scope의 초기화되기 전까지의 구간을 말한다.
즉, 선언 전에 변수를 사용(참조)하는 것을 비허용하는 개념상의 공간을 뜻한다.

let은 선언 전, 실행 컨텍스트 변수 객체에 등록되어 호이스팅이 진행되지만,
TDZ 구간에 의해 메모리 할당이 되지 않아 참조 에러가 발생한다.

```javascript
let a = 1; //전역 변수

if (true) {
  console.log(a); //ReferenceError: 정의되지 않음
  let a = 2; // 지역변수 a 선언
}
```

난 당연히 에러가 안 날 것이라고 생각했는데,(전역변수가 앞에 있으니까)
지역 변수 앞에서 참조해서 에러가 발생하는 걸 볼 수 있다.
이 부분의 경우, 지역변수 a가 호이스팅되면서 TDZ구간이 만들어졌기 때문이라고 한다.

function 같은 경우는 변수선언 3단계를 동시에 진행해버린다.
