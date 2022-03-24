---
title: "You dont know js up & going 정복하기"
date: 2021-09-24T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - javascript
  - you dont know js
---

---
title: "You dont know js up & going 정복하기"
except: "you dont knnow js!"

toc: true
toc_sticky: true

categories:
  - Javascript
tags:
  - Javascript
---

### You dont know js 1st Edition up & going 정복하기

You dont know 책을 접해보게 되었는데,  
뭔가 탄탄해보여서 읽어보기로 결정했다!

(기본 지식을 가졌다고 가정하고 정리해보겠습니다!)

#### Chapter 1: Into Programming

1. Input

   ```javascript
   let age = prompt("나이를 입력해주세요");
   console.log(age);
   ```

2. Converting Between Types

   ```javascript
   var a = "42";
   var b = Number(a);

   console.log(a); // "42"
   console.log(b); // 42
   ```

3. Comments

   comment는 what이 아닌, 이유를 설명해야 한다

   ```javascript
   // 한줄짜리 주석
   /*
    * 주석이 multiline일 때
    * 주석주석
    */
   ```

4. 소수점 몇째자리까지

   toFixed()는 String으로 만든다.

   ```javascript
   var amount = 215.9784;
   console.log(amount.toFixed(2)); // "215.98"
   ```

5. Scope

   아래처럼 각 함수마다 그들만의 scope를 가지기 때문에  
    각각 다른 결과가 나옴을 알 수 있다.

   ```javascript
   function one() {
     // this `a` only belongs to the `one()` function
     var a = 1;
     console.log(a);
   }

   function two() {
     // this `a` only belongs to the `two()` function
     var a = 2;
     console.log(a);
   }

   one(); // 1
   two(); // 2
   ```

   아래 예제에서는 다른 scope 안에 중첩될 수 있음을 보여준다.
   여기서 inner함수는 a, b에 대한 접근이 가능하지만,
   outer함수는 a에 대한 접근만 가능함을 알 수 있다.

   그런데, const 타입인 TAX_RATE는 함수 밖에 있음에도
   inner함수에서 사용 가능함을 알 수 있다.

   ```javascript
   const TAX_RATE = 0.08;

   function outer() {
     var a = 1;

     function inner() {
       var b = 2;
       // we can access both `a` and `b` here
       console.log(a + b); // 3
       console.log(a * TAX_RATE); // 0.24
     }
     inner();

     // we can only access `a` here
     console.log(a); // 1
   }

   outer();
   ```

#### Chapter 2: Into JavaScript

1. Values & Types

   ```

   [typeof 변수명]하면 나올 수 있는 부분

   - string
   - number
   - boolean
   - null and undefined
   - object
   - symbol (new to ES6)
   ```

2. Objects

   ```javascript
   var obj = {
     a: "hello world",
     b: 42,
     c: true,
   };

   // Dot 노테이션: 짧고 읽기 쉬워서 가능한 많이 사용되는 부분

   obj.a; // "hello world"
   obj.b; // 42
   obj.c; // true

   // Bracket 노테이션: property/key로 접근하고자 할 때 도움된다

   obj["a"]; // "hello world"
   obj["b"]; // 42
   obj["c"]; // true
   ```

3. Array

   배열은 거의 다 알고 있을 것 같은데,  
   array 타입이 object인 걸 한번 더 짚고 넘어가면 될 것 같다!

   ```javascript
   var arr = [1, 2, "hi"];

   arr[0]; // 1
   arr[1]; // 2
   arr[2]; // 'hi'

   typeof arr; // "object"
   ```

4. function

   함수 같은 경우, typeof foo();를 하면  
   리턴값의 타입인 number가 나오는 것이 새로웠다!

   ```javascript
   function foo() {
     return 42;
   }

   foo.bar = "hello world";

   typeof foo; // "function"
   typeof foo(); // "number"
   typeof foo.bar; // "string"
   ```

5. 내장 함수 짚고 넘어가기

   ```javascript
   var a = "hello world";
   var b = 3.14159;

   a.length; // 11
   a.toUpperCase(); // "HELLO WORLD"
   b.toFixed(4); // "3.1416"
   ```

6. Truthy & Falsy

   false로 인식될 수 있는 부분은  
   아래와 같다

   ```javascript
   - "" (empty string)
   - 0, -0, NaN (invalid number)
   - null, undefined
   - false
   ```

   true로 인식될 수 있는 부분은  
   아래와 같다.  
   빈 배열, 빈 Object도 true로 인식된다.

   ```javascript
   - "hello"
   - 42
   - true
   - [ ], [ 1, "2", 3 ] (arrays)
   - { }, { a: 42 } (objects)
   - function foo() { .. } (functions)
   ```

7. Equality

   Equality 비교 연산자로는 '==', '===', '!=', '!==', '!'가 있다.

   ```javascript
   var a = "42";
   var b = 42;

   a == b; // true
   a === b; // false(타입까지 비교)
   ```

   그런데, non-primitive 타입(즉, function, array, object)의 경우는  
   위의 부분과 좀 다르다.

   ```javascript
   var a = [1, 2, 3];
   var b = [1, 2, 3];
   var c = "1,2,3";

   a == c; // true
   b == c; // true
   a == b; // false
   ```

   references가 매칭되는지를 확인하기 때문이라고 한다.
   위에서 배열과 string이 같은 부분은,
   array 요소가 쉼표로 묶이고 최종적으로 String화 되는 부분과 같다는 걸 알 수 있다.

8. Function Scopes(함수 기반 스코프)

   - 호이스팅
     : 호이스팅은 둘러싸는 범위의 위로 이동될 때를 말한다.
     var의 경우만 나타나있는데, let/const는 TDZ에 의해서 초기화 전에 액세스하려고 하면 에러가 발생한다.
     let/const의 호이스팅은 나중에 알면 좋을 것 같다.

   ```javascript
   var a = 2;

   foo();
   // declaration 전에 사용 가능됨을 알 수 있는데,
   // 호이스팅되었음을 알 수 있다.

   function foo() {
     a = 3;
     console.log(a); // 3

     var a;
     // foo() scope위로
     // declaration이 호이스팅되었음을 알 수 있다.
   }

   console.log(a); // 2
   ```

   함수 기반 scope 예제를 보자.
   var a는 함수 안에만 있기 때문에
   함수 밖에서는 값이 안 나타남을 알 수 있다.

   ```javascript
   function foo() {
     var a = 1; // `a` not formally declared
   }

   foo();
   a; // []
   ```

9. 블록 기반 scope

   var 대신 let을 사용하면 어떨까?  
   let은 아래 예제를 보면 while{}안에 있다.  
   그래서 let c는 가장 가까운 {}문, whild문에서만 사용할 수 있음을 볼 수 있다.

   그래서 let, const는 블록기반 scope이다

   ```javascript
   function foo() {
     var a = 1;

     if (a >= 1) {
       let b = 2;

       while (b < 5) {
         let c = b * 2; // 4, 6, 8
         b++; // 3, 4, 5 -> 멈춤

         console.log(a + c);
       }
     }
   }

   foo();
   // 5 7 9
   ```

10. Strict Mode

    ES5에서는 "strict mode"가 추가되었는데,  
    rule을 더 타이트하게 함을 알 수 있다.

    더 안전하고, 가이드라인에 더 적합하고,
    더 안전한 방식을 고수하게 하며 최적화에 도움을 주므로  
    많이 사용되어야 함을 알 수 있다.

    ```javascript
    function foo() {
      "use strict"; // turn on strict mode
      a = 1; // `var` missing, ReferenceError
    }

    foo();
    ```

11. Functions As Values

    아래와 같은 구조를 많이 볼 수 있다.

    ```javascript
    var foo = function () {
      // 익명함수..
    };

    var x = function bar() {
      // 명명된 함수 표현식..
    };
    ```

    function 결과값을 변수에 할당한다.

12. 즉시 호출된 함수 표현식 (IIFEs)

    ```javascript
    (function IIFE() {
      console.log("Hello!");
    })(); // "Hello!"

    var a = 42;

    (function IIFE() {
      var a = 10;
      console.log(a); // 10
    })();

    console.log(a); // 42
    ```

    })();줄은 바로 앞에 참조된 함수 표현식을 실제로 실행함을 알 수 있다.
    () 안에서 함수가 진행되기 때문에 주변 코드에 영향이 가지 않는다.

13. \*Closure(클로저)

    클로저는 진짜 중요한 개념이다.  
    이 책에서는 깊게 파고들진 않지만 scope&Closure를 언급한다고 한다.

    closure은 "remember"로 생각할 수 있다.

    ```javascript
    function makeAdder(x) {
      // x는 내부 변수이고
      // inner 함수인 add는 x를 사용하기 때문에
      // 클로저를 가진다.
      function add(y) {
        return y + x;
      }
      return add;
    }

    // add(..) inner 함수에 대한 reference를 가진다.
    var plusOne = makeAdder(1); // y -> 1
    var plusTen = makeAdder(10); // y -> 10

    plusOne(3); // 4  <-- 1 + 3
    plusOne(41); // 42 <-- 1 + 41

    plusTen(13); // 23 <-- 10 + 13
    ```

14. Module

    클로저의 일반적인 사용은 모듈 패턴이다.  
    모듈은 외부로부터 숨겨진 private implementation details(변수, 함수) 뿐만 아니라  
    외부로부터 접근 가능한 Public API도 정의하도록 한다.

    ```javascript
    function User() {
      var username, password;
      function doLogin(user, pw) {
        username = user;
        password = pw;
        // do the rest of the login work
      }

      var publicAPI = {
        login: doLogin,
      };
      return publicAPI;
    }

    // create a `User` module instance
    var fred = User();
    fred.login("fred", "12Battery34!");
    ```

    User가 클래스가 아닌 함수로써 사용됨을 알 수 있다.  
    `User`모듈의 인스턴스를 생성함을 알 수 있고,  
    inner 변수/함수 각각을 새로 copy함을 알 수 있다.

    내부 함수 doLogin은 username과 password에 대한 클로저를 가진다.  
    그래서 User()가 running이 끝난 후에도 2개 변수에 대한 access를 지속한다.

    `publicAPI`는 object로, login: doLogin 형태를 리턴한다.
    그래서 User().login으로 User()함수는 실행을 끝내고  
    username, password는 날아가지만,  
    doLogin에는 클로저를 가지고 있기 때문에 username, password가 살아있다.  
    그래서 계속 내부 변수에 대해 접근할 수 있다.

15. this Identifier

    this는 객체 지향 패턴과 관련 있어 보이지만,  
    자바스크립트에서는 다른 메커니즘을 가진다고 한다.

    만약 함수가 this reference를 내부에 가진다면  
    이 this는 object를 주로 가리킨다고 한다.  
    하지만 object가 가리키는 것은 함수가 어떻게 호출되었는지에 달려 있다.

    가장 큰 오해로, this가 함수 그자체를 refer하지 않는다는 것을 깨닫는 것이 중요하다고 한다.

    ```javascript
    function foo() {
      console.log(this.bar);
    }

    var bar = "global";

    var obj1 = {
      bar: "obj1",
      foo: foo,
    };

    var obj2 = {
      bar: "obj2",
    };

    // --------

    foo(); // "global" - 글로벌 object의 bar
    obj1.foo(); // "obj1" - obj1의 bar
    foo.call(obj2); // "obj2" - 이 예제는 this를 obj2로 세팅한다.
    new foo(); // undefined - 새로운 빈 object를 this로 삼기 때문에 undefined이다.
    ```

16. Prototypes

    자바스크립트에서 프로토타입은 복잡하다.  
    객체 속성을 참조할 때 해당 속성이 없으면  
    자동으로 해당 객체의 내부 프로토타입 참조를 사용하여 속성을 찾을 다른 객체를 찾는다.  
    속성이 누락된 경우 이를 거의 fallback으로 생각한다.

    ```javascript
    var foo = {
      a: 42,
    };

    // create `bar` and link it to `foo`
    var bar = Object.create(foo);

    bar.b = "hello world";

    bar.b; // "hello world"
    bar.a; // 42 <-- delegated to `foo`
    ```

17. newer JavaScript: polyfilling and transpiling

    자바스크립트를 구형 브라우저에 가져오는데 사용 할 수 있는  
    `polyfilling`과 `transpiling`이 있다.

    - polyfilling: 이전 JS 환경에서 실행할 수 있는 코드 조각

    - transpiling: 최신 코드를 이전 코드로 변환하는 도구로,
      transfer + compile이 합쳐진 단어

    ```javascript
    // newer
    function foo(a = 2) {
      console.log(a);
    }

    // older
    function foo() {
      var a = arguments[0] !== void 0 ? arguments[0] : 2;
      console.log(a);
    }
    ```

    transpiler는 JS 개발 ecosystem 및 프로세스의 표준 부분으로 간주되어야 한다.  
    JS는 이전보다 훨씬 더 빠르게 계속 발전할 것이므로  
    몇 개월마다 새로운 구문과 새로운 기능이 추가될 것이라고 한다.

    기본적으로 트랜스파일러를 사용하는 경우  
    오늘날의 브라우저가 단계적으로 사라질 때까지 몇 년을 기다리지 않고  
    유용하다고 생각될 때마다 항상 새 구문으로 전환할 수 있다.

    이러한 예로는 Babel(ES6+ -> ES5로 변환),  
    Traceur(ES6,7 -> ES5)로 변환한다.

18. Non-JavaScript

    Non-JavaScript로는 DOM API가 있다.

    ```javascript
    var el = document.getElementById("foo");
    ```

    또 입/출력이 있다.

19. Hoisting 호이스팅

    위에서 호이스팅을 잠시 짚고 넘어갔다.  
    호이스팅은 함수 먼저 호이스팅된다.

    ```javascript
    foo(); // 1

    var foo;

    function foo() {
      console.log(1);
    }

    foo = function () {
      console.log(2);
    };
    ```

#### 마지막 소감

와 정말 챕터 1,2만 하는데도 힘든 것 같다.  
영어라서 그런가..  
호이스팅과 클로저는 좀더 알아봐야 될 것 같지만,  
이 책에서는  
호이스팅 - 속해있는{}의 위로 끌어올리는 것
클로저 - remember!

다음에 더 읽어야겠돠..

출처: https://github.com/getify/You-Dont-Know-JS/tree/1st-ed
