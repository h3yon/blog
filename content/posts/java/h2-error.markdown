---
title: "h2 database 에러 해결하기"
date: 2021-08-19T23:47:34+09:00
draft: false
categories:
  - database
tags:
  - database
---


### h2 database 에러 해결하기

오늘 아래의 에러가 계속 괴롭혀서 해결 방법을 써보고자 한다

```
h2 Database not found, either pre-create it or allow remote database creation (not recommended in secure environments)
```

`jdbc:h2:tcp://localhost/~/test`를 아래처럼 계속 눌렀는데,

<img src="https://user-images.githubusercontent.com/46602874/133929365-4d959b70-657d-4e6c-ae54-6fb276123e74.png">

이 에러가 계속 나타났다.

```
h2 Database not found, either pre-create it or allow remote database creation (not recommended in secure environments)
```

시도했던 방법은 아래와 같은데,  
하나같이 다 안 됐다.

```
1. English로 진행하기
2. jdbc:h2:~/test로 접속하기
3. brew install h2 설치 후 진행
```

그러다가 실제 존재하는 디렉토리 위치를 넣어주었다.  
(Mac, Window 상관 X)

<img src="https://user-images.githubusercontent.com/46602874/133929434-6e7cb778-aec9-4fbf-8eee-ea4a62e81227.png">

그럼 들어가지는데,  
database 내용이 들어가있지 않다.

이때,
`jdbc:h2:tcp://localhost/~/test`로 다시 들어가면
database 내용이 잘 나타남을 알 수 있다.

처음에는 잘 안 됐는데,  
디렉토리 위치를 넣어주고 잘 들어가지지만 DB 내용이 안 들어가있는 부분 확인 후,  
localhost/~/[DB명] 으로 들어가는 걸  
한 3번 반복해보니 잘 들어가지는 걸 알 수 있었다,,

- 반복할 때마다 h2를 껐다 키고 시도해보는 걸 추천 드립니다!!
