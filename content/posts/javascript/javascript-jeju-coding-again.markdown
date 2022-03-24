---
title: "[Javascript]자바스크립트 100문제 again"
date: 2021-09-10T23:47:34+09:00
draft: false
categories:
  - javascript
tags:
  - javascript
  - jeju coding
---


### 자바스크립트 복습

출처: 제주코딩베이스캠프

1.  중간에 추가하기

    아래처럼 만드려면?

    ```javascript
    var arr = [200, 100, 300];
    [200, 100, 10000, 300];
    ```

    답: arr.splice(2,0,10000);

2.  다음 출력 값으로 올바른 것은?

    ```javascript
    var arr = [100, 200, 300];
    console.log(typeof arr);
    ```

    답: object

3.  객체의 키 이름 중복

    ```javascript
    var d = {
      "height": 180,
      "weight": 78,
      "weight": 84,
    };
    console.log(d["weight"]);
    ```

    답: 84

4.  concat을 활용한 출력 방법

    ```javascript
    데이터
    var year = '2019';
    var month = '04';
    var day = '26';
    var hour = '11';
    var minute = '34';
    var second = '27';

    var result = //빈칸을 채워주세요

    console.log(result);


    출력
    2019/04/26 11:34:27
    ```

    답!

    ```javascript
    year.concat("/", month, "/", day, " ", hour, ":", minute, ":", second);
    ```

5.  별 찍기

    ```javascript
    입력
    5

    출력
        *
       ***
      *****
     *******
    *********
    ```

    1, 1+2, 1+2x2, 1+2x3, 1+2x4 로 늘어나는 걸 알 수 있다.

    ```javascript
    const n = prompt("숫자를 입력하세요.");
    let tree = "";

    for (let i = 1; i <= n; i++) {
      let star = "";
      for (let j = 1; j <= n - i; j++) {
        star += " ";
      }
      for (let k = 1; k <= 2 * i - 1; k++) {
        star += "*";
      }
      tree += star + "\n";
    }

    console.log(tree);
    ```

6.  1부터 100까지 모두 더하는 Code

    ```javascript
    let result = 0;

    for (let i = 0; i < 101; i++) {
      result += i;
    }
    console.log(result);
    ```

7.  다음 소스코드에서 클래스를 작성하여 게임 캐릭터의 능력치와 '파이어볼'이 출력되게 만드시오.

    ```javascript
    데이터
    <여기에 class를 작성하세요.>
    const x = new Wizard(545, 210, 10);
    console.log(x.health, x.mana, x.armor);
    x.attack();

    출력
    545 210 10
    파이어볼
    ```

    답

    ```javascript
    class Wizard {
      constructor(health, mana, armor) {
        this.health = health;
        this.mana = mana;
        this.armor = armor;
      }
      attack() {
        console.log("파이어볼");
      }
    }
    ```

8.  수성, 금성, 지구, 화성, 목성, 토성, 천왕성, 해왕성으로 총 8개.
    입력으로 행성의 순서를 나타내는 숫자 n이 입력됩니다.
    출력으로 그 순서에 해당하는 행성의 이름을 출력해 주세요.

    ```javascript
    입출력;

    입력: 1;
    출력: 수성;
    ```

    답??

    ```javascript
    const planets = ["수성", "금성", "지구", "화성", "목성", "토성", "천왕성", "해왕성"];
    const n = prompt("몇 번째 행성인가요?");
    console.log(planets[n - 1]);
    ```

9.  문장이 입력되면 거꾸로 출력하는 프로그램을 만들어 봅시다.

    답?

    ```javascript
    var stmt = prompt("입출력");
    const reverseStmt = stmt.split("").reverse().join("");
    console.log(reverseStmt);
    ```

10. 세 과목의 점수가 주어지면 전체 평균 점수. 소숫점 자리는 모두 버립니다.

    ```javascript
    const scores = prompt("세 과목의 점수를 입력하세요").split(" ");
    let sum = 0;
    scores.forEach((val) => {
      sum += parseInt(val, 10);
    });
    console.log(Math.floor(sum / 3));
    ```

11. 공백으로 구분하여 두 숫자 a와 b가 주어지면, a의 b승을 구하는 프로그램을 작성하세요.

    ```javascript
    const n = prompt("a와 b를 입력해주세요").split(" ");
    console.log(parseInt(n[0], 10) ** parseInt(n[1], 10));
    ```

12. Set을 만드는 방법?

    ```javascript
    3)  var x = new Set('javascript');
    5)  var x = new Set();
    ```

13. 우리 태양계를 이루는 행성은 수성, 금성, 지구, 화성, 목성, 토성, 천왕성, 해왕성이 있습니다.
    이 행성들의 영어 이름은 Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune입니다.
    행성의 한글 이름을 입력하면 영어 이름을 반환하는 프로그램을 만들어 주세요.

    ```javascript
    var planets = {
      "수성": "Mercury",
      "금성": "Venus",
      "지구": "Earth",
      "화성": "Mars",
      "목성": "Jupiter",
      "토성": "Saturn",
      "천왕성": "Uranus",
      "해왕성": "Neptune입니다",
    };
    const input = prompt("행성의 한글 이름을 입력해주세요");
    console.log(planets[input]);
    ```

14. 문자 pineapple에는 apple이라는 문자가 숨어 있습니다. 원범이는 이렇듯 문자열 속에 숨어있는 문자를 찾아보려고 합니다.
    첫번째 입력에서는 문자열이 입력되고, 두번째에는 찾을 문자가 입력되어야 합니다.
    그 문자가 시작하는 index를 반환하는 프로그램을 만들어 주세요

    ```javascript
    let stmt = prompt("문자열을 입력해주세요");
    let target = prompt("찾을 문자를 입력해주세요");

    console.log(stmt.indexOf(target));
    ```

15. 다음 배열 내장함수의 시간 복잡도가 O(1)이 아닌 것을 모두 고르시오.

    1. arr[i]
    2. arr.push(5)
    3. arr.slice()
    4. arr.pop()
    5. arr.includes(5)

    답: '3번', '5번'

16. 문자열을 입력받으면 단어의 갯수를 출력하는 프로그램

    ```javascript
    let stmt = prompt("문자열을 입력해주세요");
    let res = stmt.split(" ");
    console.log(res.length);
    ```

17. 한 줄에 여러개의 숫자가 입력되면, 역순으로 그 숫자들을 하나씩 출력하는 프로그램

    ```javascript
    let input = prompt("숫자들을 입력해주세요").split(" ").reverse();
    let result = "";

    input.forEach((i) => {
      result += i;
    });

    console.log(result);
    ```

18. 키가 주어지면 순서대로 제대로 섰는지 확인하는 프로그램

    ```javascript
    let unsorted = prompt("키를 입력하세요");
    let sorted = "";

    sorted = unsorted
      .split(" ")
      .sort((a, b) => a - b)
      .join(" ");
    if (unsorted === sorted) console.log("YES");
    else console.log("NO");
    ```

19. \*\*학생들이 뽑은 후보들을 입력받으면 뽑힌 학생의 이름과 받은 표 수를 출력하는 프로그램

    ```javascript
    let arr = prompt("후보를 입력해주세요").split(" ");
    let students = {};
    let winner = "";

    arr.forEach((val) => {
      students[val] = students[val] == undefined ? 1 : students[val] + 1;
    });

    winner = Object.keys(students).reduce((a, b) => {
      return students[a] > students[b] ? a : b;
    });
    console.log(`${winner}(이)가 총 ${students[winner]}표로 반장이 되었습니다.`);
    ```

20. \*\* 문장이 입력되면 모든 q를 e로 바꾸는 프로그램

    ```javascript
    /**
     * 1번째 방법
     */
    const word = prompt("문장을 입력하세요");
    function replaceAll(str, searchStr, replaceStr) {
      return str.split(searchStr).join(replaceStr);
    }
    console.log(replaceAll(word, "q", "e"));
    /**
     * 2번째 방법
     */
    const word = prompt("문장을 입력하세요");
    console.log(word.replace(/q/gi, "e"));
    ```

21. 원범이와 친구들이 총 몇 명 탈 수 있는지 알 수 있는 프로그램

    내가 푼 방식

    ```javascript
    let target = prompt("제한 무게를 입력하세요");
    let num = prompt("함께한 친구를 입력하세요");
    let weights = [];
    let sum = 0;
    let result = 0;

    for (let i in num) {
      weights += prompt("몸무게를 입력하세요");
    }

    weights.forEach((val) => {
      sum += val;
      if (sum <= target) {
        result += 1;
      }
    });
    console.log(result);
    ```

    정답. 입력받을 때 계산하면 더 빠르다.

    ```javascript
    let total = 0;
    let count = 0;
    const limit = prompt("제한 무게를 입력하세요.");
    const n = prompt("인원수를 입력하세요.");

    for (let i = 1; i <= n; i++) {
      total += parseInt(prompt("무게를 입력해주세요."), 10);
      if (total <= limit) {
        count = i;
      }
    }

    console.log(count);
    ```

22. 2020년 1월 1일은 수요일입니다. 2020년 a월 b일은 무슨 요일일까요?
    두 수 a, b를 입력받아 2020년 a월 b일이 무슨 요일인지 리턴하는 함수 solution을 완성하세요.
    요일의 이름은 일요일부터 토요일까지 각각 SUN, MON, TUE, WED, THU, FRI, SAT 입니다.

    ```javascript
    const day = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];
    let a = prompt("월을 입력하세요");
    let b = prompt("일을 입력하세요");
    const x = new Date(`2020-${a}-${b}`);
    console.log(day[x.getDay()]);
    ```

23. 10진수, 2진수, 16진수

    ```javascript
    let num = prompt("숫자를 입력하세요");
    num.toString(2); // 2진수로 변환

    parseInt("1010110", 2).toString(16); //10진수로 변환 후 다시 16진수로 변환
    ```

24. 사용자가 입력한 양의 정수의 각 자리수의 합을 구하는 프로그램

    ```javascript
    let input = prompt("수를 입력하세요");
    let sum = 0;
    for (let i in input) sum += parseInt(input[i], 10);
    console.log(sum);
    ```

25. 1부터 20까지의(20을 포함) 모든 숫자를 일렬로 놓고 모든 자릿수의 총 합을 구하세요.
    예를 들어 10부터 15까지의 모든 숫자를 일렬로 놓으면 101112131415이고
    각 자리의 숫자를 더하면 21입니다. (1+0+1+1+1+2+1+3+1+4+1+5 = 21)

    ```javascript
    let result = "";
    let sum = 0;
    for (let i = 1; i < 21; i++) {
      result += i;
    }
    for (let i in result) {
      sum += parseInt(result[i], 10);
    }
    console.log(sum);
    ```

26. set 자료형의 응용
    바울랩에서는 3월 29일 제주대학교에서 '제주 빅데이터 사회혁신 해커톤' 행사를 주최하게 되었습니다. 이에 구글 설문지를 배포하였으나 제주대학생들이 중복해서 n개씩 설문지를 제출하였습니다.
    중복된 데이터들을 삭제하여 실제 접수 명단이 몇 명인지 알고 싶습니다.
    아래 주어진 데이터들로부터 중복을 제거하여 실제 접수 인원을 출력해 주세요.

    ```javascript
    const people = {
      이호준: "01050442903",
      이호상: "01051442904",
      이준호: "01050342904",
      이호준: "01050442903",
      이준: "01050412904",
      이호: "01050443904",
      이호준: "01050442903",
    };
    // pass
    ```

    ```javascript
    const people = {
      이호준: "01050442903",
      이호상: "01051442904",
      이준호: "01050342904",
      이호준: "01050442903",
      이준: "01050412904",
      이호: "01050443904",
      이호준: "01050442903",
    };
    const result = new Set();
    for (let key in people) {
      result.add(people[key]);
    }
    console.log(result.size);
    ```

27. 버블정렬 구현
    버블정렬은 두 인접한 원소를 검사하여 정렬하는 방법을 말합니다. 시간 복잡도는 느리지만 코드가 단순하기 때문에 자주 사용됩니다. 아래 코드의 빈 칸을 채워 버블 정렬을 완성해 봅시다.

    ```javascript
    function bubble(arr) {
      let result = arr.slice();

      for (let i = 0; i < result.length - 1; i++) {
        for (/*빈칸을 채워주세요.*/) {
          if (result[j] > result[j + 1]) {
            //빈칸을 채워주세요.
          }
        }
      }
      return result;
    }

    const items = prompt('입력해주세요.').split(' ').map((n) => {
      return parseInt(n, 10);
    });

    console.log(bubble(items));
    ```

    ```javascript
    function bubble(arr) {
      let result = arr.slice();

      for (let i = 0; i < result.length - 1; i++) {
        for (let j = 0; j < result.length - i; j++) {
          if (result[j] > result[j + 1]) {
            let temp = result[j];
            result[j] = result[j + 1];
            result[j + 1] = temp;
          }
        }
      }
      return result;
    }

    const items = prompt("입력해주세요.")
      .split(" ")
      .map((n) => {
        return parseInt(n, 10);
      });

    console.log(bubble(items));
    ```
