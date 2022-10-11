---
title: "[Elasticsearch] 기초부터 다지는 ElasticSearch 운영 노하우2"
date: 2022-10-09T19:00:34+09:00
draft: false
categories:
  - elasticsearch
tags:
  - elasticsearch
---

# Elasticsearch 운영 노하우: 버전, 샤드배치

## 1. Elasticsearch Head, 모니터링 툴


<img width="999" alt="image" src="https://user-images.githubusercontent.com/46602874/194743304-5b56c46f-1f1d-41cd-9ce7-3aef5b71c532.png">

첨부된 사진처럼 [크롬 익스텐션](https://chrome.google.com/webstore/detail/multi-elasticsearch-head/cpmmilfkofbeimbmgiclohpodggeheim?hl=ko) 으로 설치하거나, [깃허브](https://github.com/mobz/elasticsearch-head) 로 설치할 수 있다. 원래는 크롬 익스텐션을 애용하였는데, 2022년 6월 기준으로 익스텐션이 `Multi Elasticsearch Head` 로 변경된 것으로 보인다.

## 2. 버전 업그레이드 종류

- Full Cluster Restart

    전체 노드를 재시작하는 방식, 다운타임 발생하지만 빠르게 버전 업그레이드 가능
- Rolling Restart  

    노드는 순차적으로 한대씩 재시작하는 방식, 다운타임은 없지만 노드 개수에 따라 소요 시간이 길어질 수 있다.

### Rolling Upgrade 순서

1. 클러스터 내 샤드 할당 기능 비활성화
    ```
    PUT _cluster/settings
    {
      "persistent": {
        "cluster.routing.allocation.enable": "none"
      }
    }'
    ```

2. 프라이머리 샤드와 레플리카 샤드 데이터 동기화
    ```
    POST _flush/synced
    ```
3. 노드 1대 버전 업그레이드 이후 클러스터 합류 확인
4. 클러스터 내 샤드 할당 기능 활성화
    ```
    PUT _cluster/settings
    {
      "persistent": {
        "cluster.routing.allocation.enable": "all"
      }
    }'
    ```
5. 클러스터 Green 상태 확인

## 3. 샤드 배치 방식

ES는 대부분 자동으로 샤드를 배치하지만, 수동으로 배치해야할 때가 있다.  
샤드 배치 방식을 변경하는 방법은 아래와 같다.

|옵션|설명|
|--|--|
|reroute|샤드 하나하나를 특정 노드에 배치할 때 사용|
|allocation|클러스터 전체에 해당하는 샤드 배치 방식 변경 시 사용|
|rebalance|클러스터 전체에 샤드 재분배 방식을 변경할 때 사용|
|filtering|특정 조건에 해당하는 샤드들을 특정 노드에 배치할 때 사용|

### 1) Reroute 방법

샤드 하나하나를 개별적으로 특정 노드에 배치할 때 사용하는 방법으로, 제어할 수 있는 동작의 예시는 아래와 같다.  
ex) 샤드 이동, 샤드 이동 취소, 레클리카 샤드의 특정 노드 할당

```
PUT _cluster/rereoute
{
  "commands": [
    {
      "move": {
        "index": "user",
        "shard": 1,
        "from_node": "data-1.es.com",
        "to_node:": "data-2.es.com"
      }
    }
  ]
}
```

### 2) Allocation 방식

클러스터 전체에 적용되는 재배치 방식

```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}'
```

`cluster.routing.allocation.enable` 방식은 아래와 같다.
- all - (default) 모든 종류의 샤드 Allocation 허용
- primaries - 프라이머리 샤드에게만 Allocation 허용
- new_primaries - 새로운 인덱스에 한해 프라이머리 샤드만 Allocation 허용
- none - 모든 샤드의 Allocation 작업 비활성화

### 3) Rebalance 방식

클러스터 내의 샤드가 배치된 후에 특정 노드에 샤드가 많다거나 배치가 고르지 않을 때 동작과 관련된 설정

```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.rebalance.enable": "replicas"
  }
}
```

`cluster.routing.rebalance.enable` 방식은 아래와 같다.

- all - (default) 모든 종류의 샤드 rebalancing 허용
- primaries - 프라이머리 샤드만 rebalancing 허용
- replicas - 레플리카 샤드만 rebalancing 허용
- none - 모든 샤드의 rebalancing 비활성화

### 4) Filtering 방식

- `cluster.routing.allocation.include.{attribute}`
(Dynamic) {attribute}에 comma로 구분된 값이 하나라도 포함하는 노드에 샤드 allocate 가능 
- `cluster.routing.allocation.require.{attribute}`
(Dynamic) {attribute}에 comma로 구분된 값이 모두 있는 노드에만 샤드 allocate 가능 
- `cluster.routing.allocation.exclude.{attribute}`
(Dynamic) {attribute}에 comma로 구분된 값이 하나라도 있는 경우 샤드 allocate X

**Attribute 예시**

```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.exclude._ip": "192.168.2.*"
  }
}
```
- _name: 노드명과 매치되는 노드
- _host_ip: Host IP 주소와 매치되는 노드
- _pulish_ip: 공인 IP 주소와 매치되는 노드
- _ip: _host_ip 또는 _publish_ip와 매치되는 노드
- _host: hostname과 매치되는 노드
- _id: 노드 id와 매치되는 노드
- _tier: 노드의 [data tier](https://www.elastic.co/guide/en/elasticsearch/reference/current/data-tiers.html) 역할과 매치되는 노드


## 4. 클러스터 설정

```
GET _cluster/settings
{
  "persistent": {},
  "transient": {}
}
```

persistent, transient 모두 값이 설정되어 있지 않을 경우 기본값으로 자동 설정된다.


- persistent
    - 영구히 적용되는 설정들
    - 클러스터 재시작해도 유지됨
- transient
    - 클러스터가 운영중인 동안에만 적용
    - 클러스터 재시작하면 초기화

## 5. 임계치 설정

```
PUT _cluster/settings
{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": "75%",
    "cluster.routing.allocation.disk.watermark.high": "85%",
    "cluster.routing.allocation.disk.watermark.flood_stage": "90%",
    "cluster.info.upate.interval": "1m"
  }
}
```

- cluster.routing.allocation.disk.threshold_enabled  
(Dynamic) Default = true. 디스크 할당 결정자를 비활성화하려면 false로 설정한다.
- cluster.routing.allocation.disk.watermark.low logo cloud  
(Dynamic) defaults to 85%. 이 수치 넘어가면 더이상 할당 X. 새롭게 생성된 인덱스에 대해서는 적용되지 않는다.
- cluster.routing.allocation.disk.watermark.high logo cloud  
(Dynamic) defaults to 90%. 임계치 넘어선 노드 대상으로 즉시 샤드 재할당. 새롭게 생성된 인덱스에 대해서도 적용된다.
- cluster.routing.allocation.disk.watermark.flood_stage logo cloud  
(Dynamic) defaults to 95%.전체 노드가 임계치를 넘으면 인덱스를 Read Only로 변경한다.

## 6. 설정 우선순위

1. transient
    : 자주 변경되는 설정
2. persistent: 자주 변경되지는 않지만 간헐적으로 변경이 필요한 설정
3. elasticsearch.yml: 노드별로 잘 변경되지 않는 설정    

## 7. Index API

|옵션|설명|
|--|--|
|open/close|인덱스의 사용 가능 결정하는 API|
|aliases|인덱스에 별칭을 부여하는 API |
|rollover|인덱스를 새로운 인덱스로 분기시키는 API|
|refresh|문서를 세그먼트로 내리는 주기를 설정하는 API|
|forcemerge|샤드 내 세그먼트를 병합시키는 API|
|reindex|인덱스를 복제하는 API|

### 1) open/close API

인덱스를 사용 가능/불가능 상태로 만드는 API
```
POST user/_close
POST user/_open
```

### 2) aliases API

인덱스에 별칭 부여 가능

```
POST _aliases/_close
{
  "actions":[
    {
      "add":{
        "index": "test*",
        "alias": "alias1"
      }
    }
  ]
}
```

- index에 특정 인덱스명을 넣어도 되고 위처럼 "test"로 시작하는 모든 인덱스를 별칭 지정할 수 있다. 근데 그러면 alias1으로 묶은 형태의 인덱스는 색인은 안 되고 검색만 된다. alias1 내 close된 인덱스가 있으면 검색 요청이 불가능하다.

### 3) rollover API

```
PUT logs-000001?pretty
{
  "aliases": { "logs_write": {} }
}


POST logs_write/_rollover
{
  "conditions": { 
  	"max_age": "7d",
  	"max_docs": 2,
  	"max_size": "5gb"
  }
}
```

