---
title: "[javascript] forEach, map, filter, reduce 총정리"
date: 2021-07-21T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - javascript
  - grammer
  - map
  - filter
  - reduce
---


### Javascript - forEach, map, filter, reduce 총정리

#### 참고 자료:

- [ES6의 map, filter, reduce 정리](https://velog.io/@decody/map-%EC%A0%95%EB%A6%AC)
- [reduce](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
- [참고 링크](https://bblog.tistory.com/300)

0. forEach
   가장 기본적인 Loop 메소드

   ```javascript
   const arr = ["a", "bb", "ccc", "ddddd"];
   var arr2 = [];
   arr.forEach(function (str) {
     arr2.push(str.length);
   });
   console.log(arr2); // [1, 2, 3, 5]]
   ```

   forEach문과 아래 map() 기능이 완전 똑같음을 알 수 있다.
   그럼 map과의 차이점이 대체 뭘까?

   forEach는 상위 scope 변수를 수정하면서 사이드 이펙트를 가진다.
   (추적하기 어려운 코드)

   map은 부모 scope 영역을 건드리지 않고 콜백함수만으로 목적을 달성한다.
   다른 영역에 간섭하지 않기 때문에 추적하기 쉽고 간결하다.
   map에 대해서 알아보자.

1. map()

   ```javascript
   const arr = ["a", "bb", "ccc", "ddddd"];
   const arr2 = arr.map((k) => k.length);
   console.log(arr2); // [1, 2, 3, 5]
   ```

   arr배열이 있었는데, 그 요소에서 글자수로 arr2 배열을 생성했음을 알 수 있다.

   ```javascript
   const arr = [1, 5, 2, 4, 10, 300, 357, 500];
   const arr2 = arr.map((k) => k % 5 === 0);
   console.log(arr2); // [ false, true, false, false, true, true, false, true ]
   ```

   두번째 예시를 보면 당연히 k % 5 = 0인 것,
   즉 5의 배수만 true, 그 외는 false로 잘 나타남을 알 수 있다.

   이렇게 조건에 대한 결과값이 배열로 선언되는 것을 알 수 있다.

2. filter()

   ```javascript
   const arr = [1, 5, 2, 4, 10, 300, 357, 500];
   const arr2 = arr.filter((k) => k % 5 === 0);
   console.log(arr2); // [ 5, 10, 300, 500 ]
   ```

   위에서 map 부분만 filter로 바꿔보자.
   그럼 조건에 맞는 것만 필터링돼서 배열로 선언되는 것을 알 수 있다.

3. reduce(callback[, initialValue])

   reduce()는 위의 map, filter, find를 대체할 수 있다.
   그래서 꼭 알아놔야 하는 것 같다.
   매개변수에 대한 설명은 아래와 같고, initialValue는 선택이다.

   <img src = "https://user-images.githubusercontent.com/46602874/126426783-7823ac11-df88-4294-9125-faaf287958ec.png">

   위의 사진에서 매개변수에 대해 더 자세한 설명을 볼 수 있다

   ```javascript
   var arr = [1, 2, 4, 5];
   var sum = arr.reduce((pre, val) => pre + val);
   console.log(sum); // 12

   // map 대체
   var arr = ["a", "bb", "ccc", "ddddd"];
   var arr2 = arr.reduce((pre, val) => {
     pre.push(val.length);
     return pre;
   }, []);
   console.log(arr2); // [1, 2, 3, 5]

   // filter 대체
   var arr = [1, 5, 2, 4, 10, 300, 357, 500];
   var arr2 = arr.reduce((pre, val) => {
     if (val % 5 == 0) {
       pre.push(val);
     }
     return pre;
   }, []);
   console.log(arr2); // [ 5, 10, 300, 500 ]

   // find 대체(pre = undefined로 하고, value를 넣어주면 더 이상 undefined가 아니니까.)
   var arr = [1, 5, 2, 4, 10, 300, 357, 500];
   var arr2 = arr.reduce((pre, val) => {
     if (typeof pre == "undefined" && val % 5 == 0) {
       pre = value;
     }
     return pre;
   }, undfined);
   console.log(arr2); // 5
   ```
