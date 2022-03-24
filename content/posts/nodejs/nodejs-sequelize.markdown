---
title: "Node.js의 ORM, sequelize"
date: 2021-08-12T23:47:34+09:00
draft: false
categories:
  - nodejs
tags:
  - nodejs
  - orm
---

---
title: "Node.js의 ORM, sequelize"
excerpt: "Node.js의 ORM, sequeliz"

toc: true
toc_sticky: true

categories:
  - Javascript
tags:
  - Javascript
---

## Node.js의 ORM, sequelize

다들 ORM에 대해서 들어봤을 것 같다.

## ORM이란?

> Object-Relational Mapping으로 객체와 관계형 데이터베이스의 데이터를 매핑(연결)시켜주는 것

ORM은 객체-관계 매핑의 줄임말이다.
객체 개념을 구현한 class와 RDB(Relational DB)에서 쓰이는 테이블을 자동으로 매핑하는 것이다.

ORM 프레임워크의 경우,

1. JPA/Hibernate
   JPA(Java Persistence API)는 자바의 ORM 기술 표준으로 인터페이스의 모음이다.
   이러한 JPA 표준 명세를 구현한 구현체가 Hibernate
2. Sequelize
   Sequelize는 Postgres, MySQL, MariaDB, SQLite를 지원하는 Promise에 기반한
   비동기로 동작하는 Node.js ORM
3. Django ORM
   파이썬 기반 프레임워크인 Django에서 자체적으로 지원하는 ORM
   정도가 있다.

Node.js로의 sequelize를 한번 보면 될 것 같다.

## sequelize 실습

```
$ npm i sequelize mysql2
$ npm i -g sequelize-cli
$ sequelize init
```

<img src="https://user-images.githubusercontent.com/46602874/129175307-0b912a6e-79bd-46ea-b39f-239cf9ad8442.png">

sequelize init을 하면
위 사진처럼 필요한 파일들과 폴더들이 알아서 설치된다.
초록색 글씨처럼 추가된 부분이 표시 되는데,

- config: 환경 설정 부분
- models/index.js: model 정의 및 관계가 설정되는 부분

설명은 위와 같다.
간단하게 코드를 한번 살펴보고 실습을 진행해보자.

1. models/index.js

   ```javascript
   const fs = require("fs");
   const path = require("path");
   const Sequelize = require("sequelize");
   const basename = path.basename(__filename);
   const env = process.env.NODE_ENV || "development";
   const config = require(__dirname + "/../config/config.json")[env];
   const db = {};

   let sequelize;
   ```

   NODE_ENV가 설정되어 있지 않다면 'development'로 진행됨을 알 수 있고,
   config.json 파일의 환경에 맞게 config를 초기화해주는 것을 볼 수 있다.

   밑에 코드를 보면 db 객체에 sequelize 패키지, 객체를 넣고 모듈로 사용함을 알 수 있다.

2. config/config.json

   ```javascript
   {
   "development": {
     "username": "root",
     "password": null,
     "database": "testdb",
     "host": "127.0.0.1",
     "dialect": "mysql"
   },
   "test": {
     "username": "root",
     "password": null,
     "database": "testdb",
     "host": "127.0.0.1",
     "dialect": "mysql"
   },
   "production": {
     "username": "root",
     "password": null,
     "database": "testdb",
     "host": "127.0.0.1",
     "dialect": "mysql"
   }
   }
   ```

   위에 보면 "database": "database\_[env]" 형식으로 되어 있는데,
   생성 또는 사용하려는 database 이름을 넣어주자.

   난 "database" 부분은 모두 "testdb"로 넣어주었다.
