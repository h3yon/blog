---
title: "[Nodejs]http2 적용과 발생한 에러"
date: 2021-07-21T23:47:34+09:00
draft: false
categories:
  - nodejs
tags:
  - nodejs
  - http2
---


### [node.js]http2 적용과 발생한 에러

리액트 웹페이지에 적용시킬
백엔드 서버를 구동하는 코드를 원래는 아래처럼 작성해놓았었다.

```javascript
# index.js
...
express().listen(PORT);
console.info(`${NODE_ENV} - API Server Start At Port ${PORT}`);
```

http2의 장점을 알고,
해당 코드를 http2로 변경해주었다.

```javascript
const express = require("./config/express");
const { readFileSync } = require("fs");
const http2 = require("http2");
const { NODE_ENV, PORT, SSL_CERT_PATH, SSL_KEY_PATH } = process.env;

const options = {
  allowHTTP1: true,
  cert: readFileSync(__dirname + SSL_CERT_PATH),
  key: readFileSync(__dirname + SSL_KEY_PATH),
};

http2.createSecureServer(options, express()).listen(PORT, () => {
  console.info(`${NODE_ENV} - API Server Start At Port ${PORT}`);
});
```

cert와 key를 발급하는 방법은 검색하면 나온다.
이렇게 쓸 줄 알았으면 정리해놓을 걸 그랬다ㅎㅎ
process.env도 잘 모르는 분들은 dotenv를 검색해보면 될 것 같습니다.

이렇게 구성해놓으면 아래처럼 postman에서는 결과가 잘 뿌려지는 걸 확인할 수 있다.

<img src="https://user-images.githubusercontent.com/46602874/130570176-61335751-30dc-498e-bf9b-b51245906841.png">

그런데 웹페이지를 들어가보면 아래처럼 통신이 잘 되지 않는 모습을 확인할 수 있다.

<img src="https://user-images.githubusercontent.com/46602874/130570474-3a480ef1-f696-4699-b332-267e41f9b711.png">

또, https://localhost:4000 으로 직접 들어가면 아래처럼 에러 메시지가 뜨고,
갑자기 서버가 꺼지는 걸 알 수 있다.

```javascript
development - API Server Start At Port 4000
_http_incoming.js:120
  if (this.socket.readable)
                  ^

TypeError: Cannot read property 'readable' of undefined
    at IncomingMessage._read (_http_incoming.js:120:19)
    at IncomingMessage.Readable.read (internal/streams/readable.js:462:10)
    at resume_ (internal/streams/readable.js:958:12)
    at processTicksAndRejections (internal/process/task_queues.js:82:21)
```

[에러 해결 위한 링크1](https://javascript.plainenglish.io/serving-hello-world-with-http2-and-express-js-4dd0ffe76860)

위의 링크에서 http2-express-bridge에 대한 이야기를 해주었고,
config의 express.js에서 `http2-express-bridge`모듈을 사용하였다.

```javascript
# config/express.js

...
const http2Express = require("http2-express-bridge");

module.exports = function () {
  const app = http2Express(express);
  // express();

  app.use(compression());
  app.use(express.json());

  ...

```

그런데 에러는 해결되지 않았고
이번에는 또 아래의 에러가 나타났다.

```javascript
index.js:103
        this._implicitHeader()
             ^

TypeError: this._implicitHeader is not a function
    at Http2ServerResponse.end (/Users/we/Documents/user/git-node-board/server/node_modules/compression/index.js:103:14)
    at Array.write (/Users/we/Documents/user/git-node-board/server/node_modules/finalhandler/index.js:297:9)
    at listener (/Users/we/Documents/user/git-node-board/server/node_modules/on-finished/index.js:169:15)
    at onFinish (/Users/we/Documents/user/git-node-board/server/node_modules/on-finished/index.js:100:5)
    at callback (/Users/we/Documents/user/git-node-board/server/node_modules/ee-first/index.js:55:10)
    at Http2ServerRequest.onevent (/Users/we/Documents/user/git-node-board/server/node_modules/ee-first/index.js:93:5)
```

한 2시간 동안 계속 검색한 것 같다...
이 에러를 고치기 위해서는
일단 node_modules/compression/index.js로 들어간다.

[에러 해결 위한 링크2](https://github.com/expressjs/compression/pull/127/commits/dbf7e73fee65375ae3ee10a75344481e44f63994)

위의 링크에 나와있는 것처럼,
`// 0824 변경` 아래 부분으로 바꿔준다.

<img src="https://user-images.githubusercontent.com/46602874/130571254-5fa03226-ccb5-4e4b-aa01-348c26d287b6.png">

그럼 백엔드단 api 링크로 들어갔을 때,
잘 나오는 모습을 알 수 있다.

<img src="https://user-images.githubusercontent.com/46602874/130571419-529ef2e7-8d89-41de-8511-eb81e7916448.png">
