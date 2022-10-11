---
title: "[Elasticsearch] 기초부터 다지는 ElasticSearch 운영 노하우1"
date: 2022-10-09T15:00:34+09:00
draft: false
categories:
  - elasticsearch
tags:
  - elasticsearch
---

# Elasticsearch 개요와 쿼리

### 1. NoSQL이란?  

- 빅데이터 환경에서 데이터가 기하급수적으로 늘어남에 따라 RDB 저장 및 관리 기술 만으로 감당하기 힘들어서 등장한 Database  
- Elasticsearch는 Document 기반의 Data 저장소
  
|RDB|NoSQL|
|--|--|
| Column과 Row 형태 | JSON, Key-Value 형태
| SQL로 질의 | REST API, 명령어로 질의 |
| 스키마 필수 | 스키마리스 |
| 부하분산 어려움 | 분산형 구조 |
    
## 2. Elasticsearch란?

루씬 기반의 오픈소스 검색 엔진으로,  
JSON 기반의 문서 저장, 검색이 가능하며 분석 작업이 가능

- 준실시간 검색 엔진
- 클러스터 구성  
   : 부하 분산 가능하며, 높은 수준의 안정성 이룰 수 있다.
- 스키마리스  
    : 입력될 데이터에 대해 미리 정의하지 않아도, 동적으로 스키마 생성 가능
- REST API  
    : REST API 쉬운 인터페이스를 제공하여 비교적 진입 장벽이 낮다.

### 1) 메서드

|HTTP 메서드 | 특징 |
|--|--|
|GET|조회|
|PUT|생성|
|POST|업데이트,조회|
|DELETE|삭제|

### 2) 간단한 명령어

1. 색인

    (간단한 색인) PUT으로 문서 색인 가능
    ```
      PUT user/_doc/1
      {
        "username": "name1"
      }
      ```
      만약, 스키마 정의 이후 다른 타입의 데이터를 넣어본다면, 스키마 충돌로 인해 문서가 저장되지 않는다.
      ```
      PUT user/_doc/1
      {
        "username": "name1",
        “age”: “11”
      }
      ```

      ![img1](https://wikidocs.net/images/page/164889/2.1.png)

      자세한 색인 과정은 위의 그림을 참고하자  
      [출처1](https://wikidocs.net/164889)

      (대량 색인) 대량의 데이터 넣기
      ```
      curl -X POST "localhost:9200/accounts/_doc/_bulk?pretty&refresh" \
      -H 'Content-Type: application/json' \
      --data-binary "@accounts.json"
      ```
2. 조회

    문서조회는 아래와 같다.
    ```
    curl -X GET "localhost:9200/user/_doc/1?pretty"
    ```

    인덱스 목록 조회는 아래와 같다.
    ```
    GET _cat/indices
    ```
3. 스키마 정보 확인
    ```
    GET user/_mappings
    ```
4. 문서 검색

    - queryString 사용하기
      ```
      GET accounts/_search?q=*
      ```
    - QueryDSL 사용하기
      ```
      GET accounts/_search
      {
        "query": {
          "match": {
            "city": "Veguita"
          }
        }
      }
      ```
5. 범위로 검색하기 & 메타데이터 확인하기

    ```
    GET accounts/_search
    {
      "query": {
        "bool": {
          "must": {"match_all": {}},
          "filter": {
            "range": {
              "age": {
                "gte": 20
              }
            }
          }
        }
      }
    }
    ```
    메타데이터 결과 중 아래는 설명을 참고하자
    - took: 검색에 소요된 시간
    - _shard.total: 검색에 참여한 사드 개수
    - hits.total: 검색 결과 갯수

6. 문서 분석하기
    ```
    GET books/_search
    {
      "size": 0,
      "aggs": {
        "group_by_state": {
          "terms": {
            "field": "topics.keyword"
          }
        }
      }
    }
    ```
    분석 작업은 `aggregation`이라고 부르고, searchAPI 기준으로 진행된다.  
    위의 쿼리는 아래와 유사하다.
    
    ```
    SELECT state, COUNT(*) FROM bank GROUP BY topic ORDER BY COUNT(*) DESC
    ```

    결과 예시
    ```
    {
      "took": 1,
      "timed_out": false,
      "_shards":{
        "total":2,
        ...
      },
      "hits":{
        "total": 10,
        "max_score": 0.0,
        "hits":[ ]
      },
      "aggregations":{
        "group_by_state":{
          "buckets": [
            {
              "key": "CS",
              "doc_count":3
            },
            {
              "key": "ES",
              "doc_count: 2
            },
            ...
          ]
        }
      }
    }
    ```

    topic 별로 몇개의 document가 있는지로 이해하면 될 것으로 보인다.

    ```
    GET books/_search
    {
      "size": 0,
      "aggs": {
        "group_by_state": {
          "terms": {
            "field": "topics.keyword"
          },
          "aggs":{
            "average_reviews":{
              "avg":{
                "field":"reviews"
              }
            }
          }
        }
      }
    }
    ```

    토픽 별로 평균 몇개의 리뷰가 생성되어 있는지 볼 수 있다.

    결과 예시
    ```
    {
      "took": 100,
      "timed_out": false,
      "_shards":{
        "total":2,
        ...
      },
      "hits":{
        "total": 10,
        "max_score": 0.0,
        "hits":[ ]
      },
      "aggregations":{
        "group_by_state":{
          "buckets": [
            {
              "key": "CS",
              "doc_count":3,
              "average_reviews":{
                "value: 8.55555
              }
            },
            {
              "key": "ES",
              "doc_count: 2,
              "average_reviews":{
                "value": 13.0
              }
            },
            ...
          ]
        }
      }
    }
    ```


## 3. 클러스터와 노드

- 클러스터란? 
    컴퓨터 혹은 구성 요소를 논리적으로 결합하여 전체를 하나의 컴퓨터 혹은 하나의 구성요소처럼 사용할 수 있게 해주는 기술
- 노드의 종류  
    |노드|설명|
    |--|--|
    |마스터노드|중심 노드로 클러스터 상태 등 메타데이터 관리(인덱스 생성, 삭제 등. 하나의 노드만 마스터노드로 선출)|
    |데이터노드|문서를 실제로 저장되며, 샤드가 배치되는 노드. 색인 작업은 리소스를 많이 소모한다.|
    |인제스트노드|문서 저장 전 사전처리, 데이터 전처리 위한 노드, 포맷 변경 위해 전처리 파이프라인 구성 및 실행 가능|
    |코디네이터노드|요청을 데이터 노드로 전달하고 데이터 노드로부터 결과를 취합하는 노드. 요청을 분산시켜주는 노드(라운드로빈 방식)|

## 4. 샤드와 세그먼트


![img](https://miro.medium.com/proxy/1*3xcgM8oZUTSV5ZVEjCRnNA.png)
[이미지 출처](https://www.google.com/url?sa=i&url=https%3A%2F%2Fjaemunbro.medium.com%2Felastic-search-%25EC%2583%25A4%25EB%2593%259C-%25EC%25B5%259C%25EC%25A0%2581%25ED%2599%2594-68062271fb64&psig=AOvVaw1n6JG44dTxiklBRDTFiBcu&ust=1665382408729000&source=images&cd=vfe&ved=0CA0QjhxqFwoTCPCE1Yq_0voCFQAAAAAdAAAAABAD)

- **샤드**는 인덱스에 색인되는 문서들이 저장되는공간
- **세그먼트**는 샤드의 데이터들을 가지고 있는 물리적인 파일이며, 불변이다.
- 문서들이 인덱스 내에 샤드별로 저장된다는 개념을 정확하게 이해하면 장애가 발생했을 때 장애의 규모를 정확하게 파악 가능
- 세그먼트 저장 전에 색인된 문서는 먼저 `시스템 메모리 버퍼 캐시`에 저장되고, 이 단계에서는 해당 문서가 검색되지 않음. 이후 ES의 `refresh` 과정을 거쳐야 디스크에 세그먼트 단위로 문서가 저장되고 해당 문서의 검색이 가능해짐

### + 세그먼트는 불변이다

- 같은 id로 PUT할 경우 응답 중에 _version이 증가한다.
- ES에서 데이터를 업데이트 또는 delete하려고 시도하면 새로운 세그먼트에 업데이트할 내용을 새롭게 쓰고, 기존 데이터는 불용 처리한다.
- ES 세그먼트는 불변이기 때문에 시간이 지나면 작은 크기의 세그먼트가 점점 늘어나서 크기가 점점 커지는 단점이 있다. 이를 해결하기 위해 ES는 백그라운드에서 세그먼트 병합을 진행한다.
- ES 백그라운드에서는 여러 작은 세그먼트들을 하나의 큰 세그먼트로 합치는 작업이 무수히 일어난다.
- 병합 시점에 실제 불용 처리한 데이터들을 실제로 삭제

### + 프라이머리 샤드와 레플리카 샤드

- 샤드는 프라이머리 샤드와 레플리카 샤드(for 안정성)로 구성
- 프라이머리 샤드는 최초 인덱스를 생성할 때 개수를 결정. 이후 변경 불가능.
- 프라이머리 샤드 만드는 알고리즘?
    샤드 번호 = Hash(Document ID) % 프라이머리 샤드 개수
- ES에서 샤드의 상태를 정상적으로 유지하고 장애 상황에서도 유실되지 않도록 하는 게 ES 클러스터 서비스의 연속성 유지하기 위해 꼭 필요한 작업
- 레플리카 샤드는 사용자의 검색 요청에도 응답할 수 있음
- 프라이머리 샤드가 사용불가 상태가 되면 레플리카 샤드가 프라이머리로 승격되고 레플리카 샤드가 다시 생김

## 5. 매핑 타입

|노드|종류|
|--|--|
|String|text,keyword|
|Numeric|long, integer, short, byte, double, float, half float, scaled float|
|Date|date|
|Boolean|boolean|
|Binary|binary|
|Range|integer_range, float_range, long_range, double_range, date_range|
