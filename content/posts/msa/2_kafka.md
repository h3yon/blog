# [TIL] KAFKA

## KAFKA 명령어 사용

- Zookeeper 및 Kafka 서버 기동
    
    ```
    $KAFKA_HOME/bin/zookeeper-server-start.sh  $KAFKA_HOME/config/zookeeper.properties
    $KAFKA_HOME/bin/kafka-server-start.sh  $KAFKA_HOME/config/server.properties
    ```

- Topic 생성
    
    - quickstart-envents 프로듀서가 메시지를 보냄
    - kafka-topics를 생성. 
    - 9092를 가지고 있는 카프카 서버에 토픽을 생성하겠다
    - partitions: 멀티클러스터링으로 구성했을 때 토픽이 전달되어있는 메시지를 몇군데 나눠서 저장할지에 대한 옵션
    ```
    $KAFKA_HOME/bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092 \ --partitions 1
    ```

- Topic 목록 확인
    
    ```
    $KAFKA_HOME/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
    ```

- Topic 정보 확인
    
    상세하게 토픽의 정보 볼 수 O
    
    ```
    $KAFKA_HOME/bin/kafka-topics.sh --describe --topic quickstart-events --bootstrap-server localhost:9092
    ```
    
- 메시지 생산

    어디에 보낼 것인지 브로커 주소 명시

    ```
    $KAFKA_HOME/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic quickstart-events
    ```
    
- 메시지 소비

    받는 쪽 부트스트랩 서버 주소 명시. 처음부터 받아오기 위해 from-beginning 옵션 사용
    
    ```
    $KAFKA_HOME/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic quickstart-events 
    \--from-beginning
    ```
    
## Kafka Connect

- 데이터를 자유롭게 export/import할 수 있는 기능  
- 코드 없이 configuration으로 데이터 이동
- Standalone/Distribution mode 지원
  -  RESTful API 통해 지원
  -  Stream 또는 Batch 형태로 데이터 전송 가능
  -  custom connector를 통한 다양한 플러그인 제공

<img width="889" alt="image" src="https://user-images.githubusercontent.com/46602874/207630130-c7999e5c-3012-469b-98a7-7e8420b963a6.png">
보낼 때 kafka connect source, 받을 때 sink 사용

- Kafka Connect 설치

    ```
    $ curl -O http://packages.confluent.io/archive/5.5/confluent-community-5.5.2-2.12.tar.gz
    $ curl -O http://packages.confluent.io/archive/6.1/confluent-community-6.1.0.tar.gz
    $ tar xvf confluent-community-6.1.0.tar.gz
    $ cd  $KAFKA_CONNECT_HOME
    ```

- Kafka Connect 실행

    ```
    $ ./bin/connect-distributed ./etc/kafka/connect-distributed.properties
    ```

JDBC Connector 설치

- https://docs.confluent.io/5.5.1/connect/kafka-connect-jdbc/index.html
- confluentinc-kafka-connect-jdbc-10.0.1.zip 
- confluent6.1.0 디렉토리의 etc/kafka/connect-distributed.properties 파일 마지막에 아래 plugin 정보 추가  
    (confluentinc-kafka-connect-jdbc-10.0.1위치 정보)
    ```
    plugin.path=[confluentinc-kafka-connect-jdbc-10.0.1 폴더]
    ```
- JdbcSourceConnector에서 MariaDB 사용하기 위해 mariadb 드라이버 복사
- ./share/java/kafka/ 폴더에 mariadb-java-client-2.7.2.jar  파일 복사












