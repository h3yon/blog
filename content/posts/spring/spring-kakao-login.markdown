---
title: "SpringFramework kakao api 구현하기"
date: 2021-07-02T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
  - oauth
---


### 스프링부트로 kakao API 구현해보기

https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api

일단, 위의 사이트에서 kakao API의 흐름을 살펴보자.
보면 인가코드를 우선 받아야 함을 알 수 있다.

<img src = "https://user-images.githubusercontent.com/46602874/124142896-9a798600-dac5-11eb-8cc3-1ebab94bd2b1.png">

일단 그러면 어플리케이션 키를 받아야 한다.
위의 이미지처럼 상단의 '내 애플리케이션' 버튼을 누른다.

<img src = "https://user-images.githubusercontent.com/46602874/124144091-9d28ab00-dac6-11eb-8d3a-f73b5e1f40e0.png">

아무 애플리케이션이나 만들고,
원하는 앱키를 복사해놓는다.

<img src = "https://user-images.githubusercontent.com/46602874/124150624-b03e7980-dacc-11eb-95a8-db6118b344b4.png">

또 옆에 플랫폼으로 들어가,
원하는 플랫폼에 대한 플랫폼을 등록한다.
위처럼 등록해도 되고, (8080번 localhost를 사용하는 경우)
만약 다 설정하였다면, '등록하러 가기'를 누른다.

<img src = "https://user-images.githubusercontent.com/46602874/124154174-33ad9a00-dad0-11eb-9488-06b69bc606ac.png">

아래 사용할 callback uri를 위에 사진처럼 넣는다.
**_ 상태 -> 활성화 필수!!! _**
원하는 uri로 작성하도록 한다.

<script src="https://gist.github.com/h3yon/640da0b3657ad27bf0e5085c7001f58e.js"></script>

설정한 uri로 requestMapping을 하여
코드를 받아올 수 있도록 준비한다.

<pre>
<code>
http://kauth.kakao.com/oauth/authorize?client_id={REST_API_KEY}&redirect_uri={REDIRECT_URI}&response_type=code
</pre>
</code>

만약 준비가 다 되었다면, \***\*서버를 구동한 후!!!\*\***
위의 {} 부분에 자신이 설정한 정보를 넣고 웹페이지로 접속한다.

<img src = "https://user-images.githubusercontent.com/46602874/124154338-5dff5780-dad0-11eb-81ac-5b9161b91ee7.png">

그러면 해당 화면이 나오고,

<img src = "https://user-images.githubusercontent.com/46602874/124154635-a1f25c80-dad0-11eb-8f66-e09dc6214970.png">

코드를 받을 수 있다.
(저는 포트를 8080 -> 8880으로 중간에 모두 바꿨습니다.)
