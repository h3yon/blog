---
title: "[Nodejs]요청 처리 관점에서 생각해보는 멀티스레드, 멀티코어프로세스"
date: 2021-07-27T23:47:34+09:00
draft: false
categories:
  - nodejs
tags:
  - nodejs
  - multi thread
  - multi core process
---


## Nodejs에서의 멀티스레드, 멀티코어프로세스

출처: [How to run many parallel HTTP requests using Node.js ?](https://www.geeksforgeeks.org/how-to-run-many-parallel-http-requests-using-node-js/)

nodejs는 싱글 스레드로, 10초가 소요되는 요청 A가 포함되는 경우,
다음 요청을 처리 하기 위해 10초를 기다리는 건 X!

NodeJS 이벤트 루프가 단일 스레드일 뿐이기 때문.
여러 클라이언트 요청을 수신하여 EventQueue에 배치.

## 이벤트루프

NodeJS는 이벤트 중심 아키텍처의 개념으로 구축되었고,
그 예로는 이벤트 리스너에 콜백함수를 등록하는 것!

NodeJS에는 요청을 수신하고 처리하는 무한 루프인 자체 eventLoop이 존재.

> EventLoop는 EventQueue의 Listener!

이벤트 루프는 여러 이벤트가 동시에 발생했을 때 어떤 순서로 콜백함수를 호출할 지 판단.
또한, 이벤트 발생 시 콜백함수를 관리.
노드가 종료될 때까지 이벤트 처리를 위한 작업을 반복하므로 루프라고 부른다.

NodeJS가 Non-Blocking I/O로 요청을 처리할 수 있는 경우,
이벤트 루프는 자체적으로 요청을 처리하고 자체적으로 응답을 클라이언트에 전송.

## 백그라운드

'노드의 어떠한 작업은 백그라운드에서 이루어진다.'라는 말을 많이 들어봤을 것이고,
setTimeout() 함수는 무조건 사용했을 것!

백그라운드는 setTimeout() 같은 타이머나 이벤트 리스너들이 대기하는 곳.

- 태스크 큐:
  이벤트 발생 후 큐 사인을 보낸다고 생각.
  태스크 큐로 타이머 또는 이벤트 리스너의 콜백함수 전송.
  불린 순서대로 줄을 서는 것.

- 만약 콜스택에 함수가 너무 많으면 task queue에 있는 함수를 바로 못 가져오는 상황이 발생해서
  setTimeout()의 시간이 정확하지 않을 수 있다.

- 노드의 I/O 작업은 백그라운드로 넘겨 동시에 처리.

## 멀티스레드, 멀티 코어 프로세스

NodeJS는 싱글 스레드지만,
때에 따라서 멀티스레드, 멀티 코어 프로세스로,
여러 요청을 병렬적으로 처리할 수 있다.

## 멀티 코어 프로세스와의 비교

NodeJS에서는 멀티 코어 프로세스를 위해서는 'cluster' 모듈이 사용된다.
모든 코어를 활용할 수 있는데,

멀티 프로세스는 다수의 프로세서(CPU)가 협력적으로 하나 이상의 작업(Task)을 동시에 처리하는 것.
멀티 코어 프로세스는 2개 이상의 프로세서를 포함한 집적회로

멀티프로세서 시스템에서는 여러 CPU 가 사용되지만, 멀티코어 프로세스는 CPU를 1개만 탑재하고 있기 때문에,
멀티코어 시스템의 비용은 멀티프로세서 시스템에 비해 낮아진다.
하나의 프로그램을 실행하는 경우 멀티코어 시스템이 더 빠르지만,
여러 프로그램을 실행하고 있는 경우는 멀티프로세서 시스템의 처리 속도가 향상

## Node.js모듈

1. Cluster
   시스템에 8개의 CPU가 있는 경우 8개의 NodeJS 인스턴스가 생성되고 모든 인스턴스에는 자체 독립 이벤트 루프가 존재.
   이제 NodeJS는 모든 요청을 병렬로 처리

2. worker_threads
   CPU 성능을 위한 최상의 솔루션은 작업자 스레드!
   이 모듈은 무거운 JavaScript 작업을 수행하는 데 유용하기 때문에 Node.js에서 사용

   -> I/O 작업에 적합하지 않지만, http/https 요청 감지, 교착 상태 감지, Isolation

   ```javascript
   function run() {
     console.log("3초 후 실행");
   }
   console.log("시작");
   setTimeout(run, 3000);
   console.log("끝");
   ```
