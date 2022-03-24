---
title: "[Javascript]자바스크립트 100문제 again2"
date: 2021-09-11T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - javascript
  - jeju coding
---

---
title: "[Javascript]자바스크립트 Again2"
excerpt: "[Javascript]자바스크립트 Again2"

toc: true
toc_sticky: true

categories:
  - Javascript
tags:
  - Javascript
---

### 자바스크립트 복습2

출처: 제주코딩베이스캠프

51. merge sort
    병합정렬(merge sort)은 대표적인 정렬 알고리즘 중 하나로 다음과 같이 동작합니다.

    > 1. 리스트의 길이가 0 또는 1이면 이미 정렬된 것으로 본다. 그렇지 않은 경우에는
    > 2. 정렬되지 않은 리스트를 절반으로 잘라 비슷한 크기의 두 부분 리스트로 나눈다.
    > 3. 각 부분 리스트를 재귀적으로 합병 정렬을 이용해 정렬한다.
    > 4. 두 부분 리스트를 다시 하나의 정렬된 리스트로 합병한다.

    출처 : 위키피디아

    다음 코드의 빈칸을 채워 병합정렬을 완성해 봅시다.

    ```javascript
    function mergeSort(arr) {
      if (arr.length <= 1) {
        return arr;
      }

      const mid = Math.floor(arr.length / 2);
      const left = arr.slice(0, mid);
      const right = arr.slice(mid);

      return merge(mergeSort(left), mergeSort(right));
    }

    function merge(left, right) {
      let result = [];

      while (left.length && right.length) {
        if (left[0] < right[0]) {
          result.push(left.shift());
        } else {
          result.push(right.shift());
        }
      }
      while (left.length) {
        result.push(left.shift());
      }
      while (right.length) {
        result.push(right.shift());
      }

      return result;
    }

    const array = prompt("배열을 입력하세요")
      .split(" ")
      .map((n) => parseInt(n, 10));

    console.log(mergeSort(array));
    ```

52. quick sort
    다음 빈 칸을 채워 퀵 정렬을 완성해주세요.

    ```javascript
    function quickSort(arr){
    if (arr.length <= 1){
      return arr;
    }

    const pivot = arr[0];
    const left = [];
    const right = [];

    for (let i=1; i<arr.length; i++){
      if(/*빈칸을 채워주세요*/){
        left.push(arr[i]);
      } else {
        right.push(arr[i]);
      }
    }
    return /*빈칸을 채워주세요*/
    }

    const array = prompt('배열을 입력하세요').split(' ').map(n => parseInt(n, 10));
    console.log(quickSort(array));
    ```

    풀어보기

    ```javascript
    function quickSort(arr) {
      if (arr.length <= 1) {
        return arr;
      }

      const pivot = arr[0]; // 기준점
      const left = [];
      const right = [];

      for (let i = 1; i < arr.length; i++) {
        if (arr[i] < pivot) {
          left.push(arr[i]);
        } else {
          right.push(arr[i]);
        }
      }
      // 이 부분 틀림
      return quickSort(left).concat(pivot, quickSort(right));
    }

    const array = prompt("배열을 입력하세요")
      .split(" ")
      .map((n) => parseInt(n, 10));
    console.log(quickSort(array));
    ```

53. 입력으로 주어진 괄호 문자열이 바른 문자열인지 바르지 않은 문자열인지 "YES"와 "NO"로 구분된 문자열을 출력해보자.
    ())와 같은 문자열은 바른 문자열이지만 ()()) 와 같은 문자열은 바르지 않은 문자열이다.
    (해당 문제에서는 소괄호만 판별하지만, 중괄호와 대괄호까지 판별해 보세요.)

    ```javascript
    function mathBrackets(e) {
      let count = 0;
      let 괄호 = [];

      for (let i in e) {
        if (e[i] === "(") {
          count++;
          괄호.push("(");
        }
        if (e[i] === ")") {
          count--;
          if (괄호.length == 0) return false;
          괄호.pop();
        }
      }
      if (count !== 0) return false;
      return true;
    }

    const n = prompt("문자열을 입력해주세요").split("");

    if (mathBrackets(n)) console.log("YES");
    else console.log("NO");
    ```

54. 숫자 스탬프는 매일 그 수와 스탬프에 적힌 숫자가 바뀌지만 그 숫자는 항상 연속된다.
    스탬프에 적힌 숫자가 공백으로 구분되어 주어지면 이 숫자가 연속수인지 아닌지 "YES"와 "NO"로 판별하는 프로그램을 작성하시오

    ```javascript
    function sol(l) {
      l.sort((a, b) => {
        return a - b;
      });

      for (let i = 0; i < l.length - 1; i++) {
        console.log(i);
        if (l[i] + 1 !== l[i + 1]) console.log("NO");
      }
      return "YES";
    }

    let nums = prompt("숫자를 입력하세요")
      .split(" ")
      .map((n) => parseInt(n, 10));
    console.log(sol(nums));
    ```

55. \*\*하노이의 탑

    1. 처음에 모든 원판은 A 기둥에 꽂혀 있다.
    2. 모든 원판의 지름은 다르다.
    3. 이 원반은 세 개의 기둥 중 하나에 반드시 꽂혀야 한다.
    4. 작은 원반 위에 큰 원반을 놓을 수 없다.
    5. 한 번에 하나의 원판(가장 위에 있는 원판)만을 옮길 수 있다.

    이 규칙을 만족하며 A 기둥에 있는 원반 N 개를 모두 C 원반으로 옮기고 싶습니다.
    모든 원반을 옮기기 위해 실행되어야 할 최소 원반 이동 횟수를 계산하는 프로그램을 완성해 주세요.

    ```javascript
    const route = [];

    function hanoi(num, start, end, temp) {
      //원판이 한 개일 때에는 바로 옮기면 됩니다.
      if (num === 1) {
        route.push([start, end]);
        return NaN;
      }

      //원반이 n-1개를 경유기둥으로 옮기고
      hanoi(/*내용을 채워주세요.*/);
      //가장 큰 원반은 목표기둥으로
      route.push(/*내용을 채워주세요.*/);
      //경유기둥과 시작기둥을 바꿉니다.
      hanoi(/*내용을 채워주세요.*/);
    }

    hanoi(3, "A", "B", "C");
    console.log(route);
    console.log(route.length);
    ```

    한번 풀어보기

    ```javascript
    const route = [];

    function hanoi(num, start, end, temp) {
      //원판이 한 개일 때에는 바로 옮기면 됩니다.
      if (num === 1) {
        route.push([start, end]);
        return NaN;
      }

      //원반이 n-1개를 경유기둥으로 옮기고
      hanoi(num - 1, start, temp, end);
      //가장 큰 원반은 목표기둥으로
      route.push([start, end]);
      //경유기둥과 시작기둥을 바꿉니다.
      hanoi(num - 1, temp, end, start);
    }

    hanoi(3, "A", "B", "C");
    console.log(route);
    console.log(route.length);
    ```

56. 객체의 함수 응용
    한국의 면적과 가장 비슷한 국가와 그 차이를 출력하세요.

    ```javascript
    데이터
    nationWidth = {
         'korea': 220877,
         'Rusia': 17098242,
         'China': 9596961,
         'France': 543965,
         'Japan': 377915,
        'England' : 242900,
    }

    출력
    England 22023
    ```

    풀어보기

    ```javascript
    let nationWidth = {
      "korea": 220877,
      "Rusia": 17098242,
      "China": 9596961,
      "France": 543965,
      "Japan": 377915,
      "England": 242900,
    };

    const w = nationWidth["korea"];
    delete nationWidth["korea"];

    const entry = Object.entries(nationWidth);
    const values = Object.values(nationWidth);

    let gap = Math.max.apply(null, values);
    let item = [];

    for (let i in entry) {
      if (gap > Math.abs(entry[i][1] - w)) {
        gap = Math.abs(entry[i][1] - w);
        item = entry[i];
      }
    }

    console.log(item[0], item[1] - w);
    ```

57. 0부터 1000까지 1의 개수를 세는 프로그램
    예를 들어 0부터 20까지 1의 개수를 세어본다면 1, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19에 각각 1이 들어가므로 12개의 1이 있게 됩니다.

    ```javascript
    let str = "";
    let count = 0;
    for (i = 0; i < 1001; i++) {
      str += i;
    }
    for (let j in str) {
      if (str[j] == 1) count++;
    }
    console.log(count);
    ```

58. \*\*콤마 찍기
    숫자를 입력받고 천 단위로 콤마(,)를 찍어주세요.

    ```javascript
    /**
     * 1. 내장함수 사용 방식
     */
    let num = prompt("숫자를 입력하세요").parseInt(n, 10);
    console.log(n.toLocaleString());

    /**
     * 2. 재귀함수 사용 방식
     */
    function comma(str) {
      if (str.length <= 3) return s;
      else return comma(str.slice(0, sstrlength - 3)) + "," + str.slice(str.length - 3);
    }
    console.log(comma(num));
    ```

59. \*\* 빈칸 채우기
    총 문자열의 길이는 50으로 제한하고 사용자가 문자열을 입력하면 그 문자열을 가운데 정렬을 해주고, 나머지 빈 부분에는 '='을 채워 넣어주세요.

    ```javascript
    let str = prompt("문자열을 입력하세요");
    const n = 25 + parseInt(str.length / 2, 10);

    //왼쪽부터 채우기
    const a = str.padStart(n, "=");
    //오른쪽까지 채워서 출력
    console.log(a.padEnd(50, "="));

    //padStart(길이, 채울 문자열) : 주어진 길이만큼 원래 문자열의 왼쪽부터 주어진 문자열로 채움
    //padEnd(길이, 채울 문자열) : 주어진 길이만큼 원래 문자열의 오른쪽부터 주어진 문자열로 채움
    ```

60. 아래처럼 출력하게 짜보기(이름순)

    ```javascript
    데이터
    students = ['강은지','김유정','박현서','최성훈','홍유진','박지호','권윤일','김채리','한지호','김진이','김민호','강채연']
    출력
    번호: 1, 이름: 강은지
    번호: 2, 이름: 강채연
    번호: 3, 이름: 권윤일
    번호: 4, 이름: 김민호
    번호: 5, 이름: 김유정
    번호: 6, 이름: 김진이
    번호: 7, 이름: 김채리
    번호: 8, 이름: 박지호
    번호: 9, 이름: 박현서
    번호: 10, 이름: 최성훈
    번호: 11, 이름: 한지호
    번호: 12, 이름: 홍유진
    ```

    풀어보기!

    ```javascript
    let students = ["강은지", "김유정", "박현서", "최성훈", "홍유진", "박지호", "권윤일", "김채리", "한지호", "김진이", "김민호", "강채연"];
    students.sort();
    students.map((k, v) => {
      console.log(`번호: ${v + 1} 이름: ${k}`);
    });
    ```

61. \*\* 문자열을 입력받고 연속되는 문자열을 압축해서 표현

    ```javascript
    입력;
    aaabbbbcdddd;

    출력;
    a3b4c1d4;
    ```

    풀어보기!

    ```javascript
    let input = prompt("입력하세요").join("");
    let result = "";
    let store = input[0];
    let count = 0;

    for (let i of input) {
      if (i == store) {
        count += 1;
      } else {
        result += store + String(count);
        store = i;
        count = 1;
      }
    }

    result += store + String(count);
    console.log(result);
    ```

62. 교수님이 줄임말을 배우기 위해 아래와 같이 어떤 입력이 주어지면 앞 글자만 줄여 출력

    ```javascript
    입력
    복잡한 세상 편하게 살자
    출력
    복세편살
    ```

    풀어보기!

    ```javascript
    let stmt = prompt("입력하세요").split(" ");
    let result = "";

    for (let i in stmt) {
      result += stmt[i][0];
    }
    console.log(result);
    ```

63. \*\* 이상한 엘레베이터
    정량 N에 정확히 맞춰야만 움직이는 화물용 엘리베이터가 있습니다.
    화물은 7kg, 3kg 두 가지이며 팔이 아픈 은후는 가장 적게 화물을 옮기고 싶습니다.
    가장 적게 옮길 수 있는 횟수를 출력합니다.
    만약 어떻게 해도 정량이 N이 되지 않는다면 -1을 출력합니다.

    ```javascript
    let n = prompt("n을 입력하세요");
    let cnt = 0;

    while (true) {
      if (n % 7 == 0) {
        result += parseInt(N / 7, 10);
        console.log(result);
        break;
      }
      N -= 3;
      result += 1;
      if (N < 0) {
        console.log(-1);
        break;
      }
    }
    ```

64. a = [1, 2, 3, 4]
    b = [a, b, c, d]
    이런 리스트가 있을 때 **[[1, a], [b, 2], [3, c], [d, 4]]** 이런 식으로 a, b 리스트가 번갈아가면서 출력되게 해주세요.

    ```javascript
    let a = [1, 2, 3, 4];
    let b = ["a", "b", "c", "d"];
    let c = [];

    a.map((item, idx) => {
      if (item % 2 == 0) c.push([b[idx], item]);
      else c.push([item, b[idx]]);
    });

    return c;
    ```

65. 문제66 : 블럭탑쌓기
    탑을 쌓기 위해 각 크기별로 준비된 블럭들을 정해진 순서에 맞게 쌓아야 합니다.
    순서에 맞게 쌓지 않으면 무너질 수 있습니다.

    예를 들면 정해진 순서가 BAC 라면 A 다음 C가 쌓아져야 합니다.
    선행으로 쌓아야 하는 블럭이 만족된 경우라면 탑이 무너지지 않습니다.

    - B를 쌓지 않아도 A와 C를 쌓을 수 있습니다.
    - B 다음 블럭이 C가 될 수 있습니다.

    쌓아져 있는 블럭 탑이 순서에 맞게 쌓아져 있는지 확인하세요.

    1. 블럭은 알파벳 대문자로 표기합니다.
    2. 규칙에 없는 블럭이 사용될 수 있습니다.
    3. 중복된 블럭은 존재하지 않습니다.

    ```javascript
    입력;
    탑 = ["ABCDEF", "BCAD", "ADEFQRX", "BEDFG", "EFGHZ"];
    규칙 = "ABD";

    출력[("가능", "불가능", "가능", "가능", "가능")];
    ```
