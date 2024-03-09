---
title: "스프링 배치 성능 생각해보기 - Scaling and Parallel Processing"
date: 2024-03-02T23:47:34+09:00
draft: false
categories:
  - spring-batch
tags:
  - spring-batch
---


# 스프링 배치 성능 생각해보기 - Scaling and Parallel Processing

# 우리가 아는 Chunk-oriented process

<img width="650" alt="image" src="https://github.com/h3yon/blog/assets/46602874/296849d1-4eb0-4a93-9f7d-12bbc6735084">
https://godekdls.github.io/SpringBatch/configuringastep/#514-configuring-a-step-for-restart

<img width="650" alt="image" src="https://github.com/h3yon/blog/assets/46602874/668fb7dc-f92e-4c58-9f1b-6f33b9322f8d">
https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html
  
4.x 버전까지는 첫번째의 그림이었지만,
spring batch 5.x 부터는 두번째처럼 바뀌었다.
  
chunk size만큼 read 다 하고,
for문으로 read 한 거 하나하나 process
  
그걸 한꺼번에 write
  
```jsx
//https://docs.spring.io/spring-batch/reference/step/chunk-oriented-processing.html

List items = new Arraylist();
for(int i = 0; i < commitInterval; i++){
    Object item = ***itemReader.read()***;
    if (item != null) {
        items.add(item);
    }
}

List processedItems = new Arraylist();
for(Object item: items){
    Object processedItem = ***itemProcessor.process(item)***;
    if (processedItem != null) {
        processedItems.add(processedItem);
    }
}

***itemWriter.write(processedItems)***;
```


# 성능을 고려해보자 -> **Scaling and Parallel Processing**  

[https://godekdls.github.io/Spring Batch/scalingandparallelprocessing/](https://godekdls.github.io/Spring%20Batch/scalingandparallelprocessing/)
  
이번 년도 작업한 배치의 처리량은 한번에 많아봤자 1~2만 개.  
만약 수십, 수백만 데이터를 처리하고 싶을 때는?  
더 빠른 처리를 해야할 때에는?  
  
위처럼 scailing and parallel processing 글을 참고하면 좋을 것 같다.  
  
1. 싱글 프로세스
    1. multi-threaded step → **병렬 처리**
        
        TaskExecutor 추가
        
        ```jsx
        @Bean
        public TaskExecutor taskExecutor(){
            return new SimpleAsyncTaskExecutor("spring_batch");
        }
        
        @Bean
        public Step sampleStep(TaskExecutor taskExecutor) {
        	return this.stepBuilderFactory.get("sampleStep")
        				.<String, String>chunk(10)
        				.reader(itemReader())
        				.writer(itemWriter())
        				.***taskExecutor(taskExecutor)***
        				.build();
        }
        ```
        
        <img width="896" alt="Untitled 3" src="https://github.com/h3yon/blog/assets/46602874/0c1bb583-f94b-4b87-9f76-e57b0e5626bd">

        <img width="889" alt="Untitled 4" src="https://github.com/h3yon/blog/assets/46602874/c86038ab-ed80-439e-87f5-a4943861433b">
        
        → 이렇듯 멀티스레드에서 사용하려면 동시성을 고려해야 하고, thread safe 하지 않은 리더는 `SynchronizedItemStreamReader`를 사용하거나 `동기화 해주는 객체`를 만들어 reader에 위임하자
        
    2. parallel steps
        
        ```java
        @Bean
        public Flow splitFlow() {
            return new FlowBuilder<SimpleFlow>("splitFlow")
                .split(***taskExecutor()***)
                .add(**flow1**(), **flow2**()) *// 각각 step1,step2 / step3 작업을 병렬화 가능*
                .build();
        }
        
        @Bean
        public Flow **flow1**() {
            return new FlowBuilder<SimpleFlow>("flow1")
                .start(step1())
                .next(step2())
                .build();
        }
        
        @Bean
        public Flow flow2() {
            return new FlowBuilder<SimpleFlow>("flow2")
                .start(step3())
                .build();
        }
        
        @Bean
        public TaskExecutor taskExecutor(){
            */* 디폴트는 SyncTaskExecutor지만 병렬로 실행하려면 비동기 TaskExecutor 사용 필요 */*
            return new SimpleAsyncTaskExecutor("spring_batch");
        }
        
        ```
        
2. 멀티 프로세스
    1. remote chunking of step
        
        ![Untitled 5](https://github.com/h3yon/blog/assets/46602874/c9f97d1e-f111-4784-b556-49ac729b0c9c)

        
        - Manager 컴포넌트: `싱글` 프로세스
        - Worker: `멀티 리모트` 프로세스
        - 해당 패턴은 매니저에 병목이 없어야 가장 잘 동작하므로, 
        반드시 `process가 read 보다 훨씬 무거운 작업`일 때 사용해야 한다.
        
        chunkProvider, chunkProcessor 사용
        

<img width="1942" alt="Untitled 6" src="https://github.com/h3yon/blog/assets/46602874/35790fb5-c2c5-4338-a20e-25309a7965f5">


위 구현 코드를 보면 chunkProvider과 chunkProcessor이 대충 어떻게 동작하는지 알 수 있다.

- ChunkProvider
    - item을 read
- ChunkProcessor
    - item을 process하고 마지막에 write

→ 이미 있는 reader, processor, writer를 사용 가능하다.
아이템을 동적으로 나누고 미들웨어로 작업을 공유하기 때문에 자동으로 로드밸런싱할 수 있다.

## partitioning a step (싱글 또는 멀티 프로세스)
→ 요 방법을 주로 사용 (+taskExecutor)

![Untitled 7](https://github.com/h3yon/blog/assets/46602874/aeb01ec9-156b-4e45-b57c-42bcf7ccf774)


- PartitionStep: `실행을 주도`
- Step: `remote worker`. step에 많은 오브젝트 또는 프로세스가 O

관련 구현 코드는 아래와 같다.

<img width="2062" alt="Untitled 8" src="https://github.com/h3yon/blog/assets/46602874/af0043ef-b36b-4bb7-ae6d-b0d386bca425">


- stepSplitter가 masterStepExecution 을 넘겨주며 split을 시도한다.
- split 의 getContexts를 보면 partitioner.partion을 통해 gridSize 만큼의 stepExecution을 만든다.

**그리드가 파티션 갯수?**

- 근데 여기서 gridSize가 좀 헷갈린다.
    
    Slaver Step에서 사용할 Step Execution은 gridSize만큼 생성된다. gridSize는 파티션을 나누는 사이즌데, `쓰레드 갯수`로 이해하면 된다. 
    

사용되는 코드를 보자. 보통 아래처럼 사용된다.

```java
@Bean
public Step step1Manager() {
    return stepBuilderFactory.get("step1.manager")
        .<String, String>partitioner("step1", partitioner())
        .step(step1())
        .gridSize(10)
        .taskExecutor(taskExecutor())
        .build();
}
```

<img width="772" alt="Untitled 9" src="https://github.com/h3yon/blog/assets/46602874/ab35a221-e105-4252-9451-329d6689883f">


요렇다구 한다~!

**파티셔너를 사용하는 코드 예시**
[https://jojoldu.tistory.com/550](https://jojoldu.tistory.com/550)
