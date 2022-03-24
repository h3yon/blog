---
title: "MySQL User 추가 및 권한 부여하기"
date: 2021-07-11T23:47:34+09:00
draft: false
categories:
  - MySQL
tags:
  - mysql
---


<h4>MySQL User 추가 및 권한 부여하기</h4>

<h5> mysql 사용자 추가하기 </h5>

1. 사용자 추가(비밀번호 X)
   $ create user 사용자;

2. 사용자 추가 및 패스워드 설정
   $ create user 사용자@localhost identified by 비밀번호;

3. 사용자 추가 및 패스워드 설정부터 외부에서의 접근 허용
   $ create user 사용자@'%' identified by 비밀번호;

<h5>사용자 삭제1</h5>

```javascript
    $ drop user 사용자;
```

<h5>사용자 목록 확인</h5>

    $ select User from user;

<h5>사용자에게 데이터베이스 사용권한 부여</h5>

1.  DB의 사용자에게 외부 권한 부여하기, 모든 권한 부여
    $ GRANT ALL privileges ON DB명.\* TO 사용자@'%' IDENTIFIED BY 비밀번호;
    % 대신 200.100.%으로 하면 200.100.X.X로 시작되는 IP에 원겹접속 허용

2.  DB의 사용자에게 localhost 권한 부여하기, 모든 권한 부여
    $ GRANT ALL privileges ON DB명.\* TO 사용자@localhost IDENTIFIED BY 비밀번호;

3.  localhost의 사용자에게 myDatabase의 모든 테이블 select, insert, update 권한만 부여
    $ GRANT select, insert, update on myDatabase.\* TO 사용자@localhost;

4.  권한 적용하기
    $ flush privileges;

<h5>사용자에게 부여된 권한 확인 및 제거</h5>

1. testUser 권한 보기(localhost, %, 200.100.100.0 ip 주소)
   $ SHOW GRANTS FOR testUser@localhost;
   $ SHOW GRANTS FOR testUser@'%';
   $ SHOW GRANTS FOR testUser@'200.100.100.0';

2. 사용권한 제거
   $ revoke all on DB명.테이블명 from 사용자;

<h5>사용자 삭제2</h5>
- 권한의 사용자 삭제
    $ drop user 사용자@'%';
    $ drop user 사용자@localhost;
