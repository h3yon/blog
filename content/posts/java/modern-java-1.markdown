---
title: "모던 자바 인 액션 1~6장 스트림"
date: 2022-04-26T01:50:34+09:00
draft: false
categories:
  - java
tags:
  - java
---

# 모던 자바 인 액션 1~6장 스트림

아는 내용은 정리 생략해서  
자세한 내용을 원하시는 분은 다른 포스팅 보기를 추천 드립니다!

## 1장 자바 8, 9, 10, 11?

자바 8에서는 메서드를 값으로 취급할 수 있도록 했다. 이 기능은 스트림 같은 다른 기능의 토대를 제공했다.

- 외부 반복?  
  기존 컬렉션 API에서 for-each로 반복 과정 직접 처리
- 내부 반복?  
  스트림 API 사용함으로써 API 내부에서 모든 데이터가 처리된다.

자바 8에서는 Optional도 제공한다.

## 2장 동작 파라미터화 코드 전달하기

> 동작 파라미터?  
> : 아직은 어떻게 실행할 것인지 결정하지 않은 코드

동작 파라미터화를 사용해서 원하는 사과를 필터링한다고 가정한다.


1. for 문으로 색 파라미터화하면서 원하는 사과 찾기
    ```java
    public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) { 
    List<Apple> result = new ArrayList<>(); 
    for (Apple apple : inventory) { 
        if (apple.getColor() == color) { 
        result.add(apple); 
        } 
    } 
    return result; 
    }
    ```
2. Predicate를 사용해서 각 필터링을 다르게 하는 방법
    ![image](https://user-images.githubusercontent.com/46602874/165311593-191097b0-9c2f-4300-9fcb-5bd12676b0b9.png)

3. 익명 클래스 또는 람다식 사용
    ```java
    List<Apple> result = filterApples(inventory, 
        (Apple apple) -> RED.equals(apple.getColor())
    );
    ```

## 3장 람다 표현식

> `함수형 인터페이스` = 정확히 `하나의 추상 메서드`를 지정하는 인터페이스 ex) Comparator, Runnable etc  

람다 표현식으로 추상메서드 구현을 직접 전달할 수 있다.

### 람다 활용: 실행 어라운드 패턴

> 실행 어라운드 패턴: 설정-작업-형식의 코드. 예시는 아래와 같다.

```java
public String processFile() throws IOException { 
    try (BufferedReader br = new BufferReader(new FileReader("data.txt"))) { 
        return br.readline();
    } 
}
```

![image](https://user-images.githubusercontent.com/46602874/165314253-adac2d91-09b2-4962-8965-79ae6052f559.png)

### 함수형 인터페이스 사용

- 함수 디스크립터: 함수형 인터페이스의 추상 메서드 시그니처

1. Predicate 인터페이스  
    test 추상 메서드를 정의한다. boolean을 반환한다.
2. Consumer 인터페이스
    accept 추상 메서드를 정의한다. 객체를 받아 어떤 동작을 수행하고 싶을 때 사용할 수 있다.
3. Function 인터페이스
    apply 추상 메서드를 정의한다. 입력을 받아 출력으로 매핑하는 람다를 정의할 때 활용 가능하다.

### 메서드 참조

메서드 참조를 활용해 정렬할 경우,  
1번 코드를 3번 코드로 사용할 수 있다.

```java
//1번
inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()));
//2번
inventory.sort(comparing(apple -> apple.getWeight());
//3번
inventory.sort(comparing(Apple::getWeight));
```

## 4장 스트림 소개

왼쪽 자바7, 오른쪽 자바8을 봐보자.
<img width="1163" alt="image" src="https://user-images.githubusercontent.com/46602874/165319768-a9b7f4fb-539c-4d82-98fc-ccf8d28d97d8.png">

이걸 보고 스트림이 중요함을 한번 더 깨달을 수 있었다!
스트림에는 2가지 중요 특징이 있다.

1. 파이프라이닝
    스트림 연산끼리 연결해서 스트림 자신을 반환한다. 그 덕분에 laziness, short circuiting 같은 `최적화`도 얻을 수 있다.
2. 내부 반복
    반복자로 반복하는 컬렉션과 달리 스트림은 `내부 반복`을 지원한다.

## 5장 스트림 활용

1. 필터링
2. 고유 요소(distinct)
3. 슬라이싱
   takeWhile, dropWhile(java9) -> 반복 작업 중단 가능
4. limit, skip
5. 각 요소에 함수 적용 (map)
6. 스트림 안에 map하면서 배열 생길 경우 -> flatMap
7. 일치하는지 검사(anyMatch(), allMatch(), noneMatch())
8. 요소 검색(findAny(), findFirst())
9. reducing
    함수형 프로그래밍에서는 `fold`라 부른다. 초기값을 정해서 연산할 수 있다. 초기값 없으면 Optional! 최대값 찾을 때 사용도 가능하다. ex) ...stream().reduce(Integer::max);
10. rangeClosed(1, 100)  
    range 메서드는 시작값과 종료값이 결과에 포함되지 않지만, rangeClosed는 결과에 포함된다.

### 배열로 스트림 만들기

```java
int[] numbers = {1, 2, 3, 4, 5, 6};
int sum = Arrays.stream(numbers).sum();
```

이렇게 Array에 대해 스트림을 만들 수 있다.

### 무한 스트림 만들기

```java
Stream.iterate(0, n -> n + 2)
   .limit(10)
   .forEach(System.out::println);
```

iterate 메서드는 새로운 값을 끊임없이 생산할 수 있다.  
이걸 `unbound 스트림`, `무한 스트림` 이라고 한다.

또 filter는 언제 작업을 중단해야 하는지 알 수 없다.  
`takeWhile` 메서드를 사용하면 중단 가능하다.

 

#### generate 메서드

```java
Stream.generate(Math::random)
  .limit(5)
  .forEach(System.out::println);
```

generate는 생산된 각 값을 연속적으로 계산하는 게 아니라, Supplier<T>를 인수로 받아서 새로운 값을 생산한다.


## 6장 스트림으로 데이터 수집

마지막 연산을 살펴보자.

1. counting 메서드 ex) people.stream().count()
2. 최대값과 최소값 검색
    ```java
    Comparator<Person> ageComparator = Comparator.comparingInt(Personn::getAge); 
    Optional<Person> olderPerson = people.stream().collect(maxBy(ageComparator));
    ```
3. 요약 연산
    ```java
    int totalAge = people.stream().collect(summingInt(Person::getAge));
    ```
    `summarizingInt`,`summingInt` 를 사용해 합계 외에도 다양한 연산도 요약 기능으로 제공된다.
4. 문자열 연결
   ```java
   String peopleNames = people.stream().map(Person::getAge).collect(joining(","));
   ```
5. 리듀싱 요약 연산
    ```java
    int totalAge = people.stream().collect(reducing(0, Person::getAge, (i, j) -> i + j);
    int totalAge = people.stream().collect(reducing(0, Person::getAge, Integer::sum));
    ```
6. 그룹핑된 요소 조작
    ```java
    Map<Person, Job, List<Person>> peopleByJob = people.stream().collect(groupingBy(Person::getJob)); 
    //peopleByJob : {DESIGNER=[sally, daimon], DEVELOPER = [ten, med], OTHERS=[hwasa, lisa, jason, laim]}
    ```
    직업에 따라 그룹핑할 수 있다.  
    age가 30 이상인 사람만 필터링하고 싶을 때 그냥 filter(조건)을 사용하면 해당하지 않는 Job은 아예 보이지 않는다.
    ```java
    Map<Person, Job, List<Person>> peopleByJob = 
        people.stream()
            .collect(groupingBy(Person::getJob), filtering(dish -> getCalrories() > 500, toList())); 
    //peopleByJob : {DESIGNER=[sally, daimon], DEVELOPER = [ten, med], OTHERS=[]}
    ```
    그럼 조건에 맞지 않는 사람들도 잘 필터링 되었고, OTHERS도 잘 보이는 걸 알 수 있다.
    ```java
    Map<Person, Map<Salary, Job>, List<Person>> peopleByJob = 
        people.stream()
            .collect(groupingBy(Person::getJob), 
            groupingBy(person -> { 
                if (person.getSalary() <= 2500) return Salary.A; 
                else if (person.getSalary() <= 5000) return Salary.B; 
                else return Salary.C; })
            ); 
    //{DESIGNER={A=[sally], B=[daimon]}, DEVELOPER={A=[ten], B=[med]}, OTHERS={...}}
    ```
7. 서브 그룹으로 데이터 수집
    ```java
    Map<Person.Job, Long> jobCount = people.stream().collect(groupingBy(Person::getJob, counting()));
    //{DESIGNER=2, DEVELOPER=2, OTHERS=4}

    Map<Person.Job, Optional<Person>> mostSalaryByJob = 
        people.stream()
            .collect(groupingBy(Person::getJob, maxBy(CompaingInt(Person::getSalaries))));
    //{FISH=Optional[sally], OTHERS=Optional[ten], MEAT=Optional[hwasa]}
    ```
    groupingBy로 max, counting도 사용할 수 있다. 
8. 분할
    참 또는 거짓을 갖는 두 개의 그룹으로 분류할 수 있다.
    ```java
    Map<Boolean, List<Dish>> partitionedMenu = menu.stream().collect(partitioningBy(Dish::isVegetarian)); 
    // {false=[pork, beef, chicken, prawns, salmon], 
    // true=[french fires, rice, season fruit, pizza]}
    ```
    스트림 리스트를 유지한다는 장점이 있다.

    collectingAndThen을 사용함으로써 칼로리가 가장 높은 요리도 뽑을 수 있다.
    ```java
    Map<Boolean, Dish> mostCaloricPartitionedByVegetarian =
        menu.stream
            .collect(partitioningBy(Dish::isVegeterian, 
                collectingAndThen(maxBy(comparingInt(Dish::getCalories)), Optional::get))
            );
    // {false=pork, true=pizza}
    ```
9. 소수와 비소수로 분할하기
    ```java
    public boolean isPrime(int candidate){
        return IntStream.range(2, candidate)
            .noneMatch(i -> candidate % i == 0);
    }
    ```

### 6.5 Collector 인터페이스

```java
public interface Collector<T, A, R> {
  Supplier<A> supplier();
  BiConsumer<A, T> accumulator();
  Function<A, R> finisher();
  BinaryOperator<A> combiner();
  Set<Characteristics> characteristics();
}
```

1. Supplier: 반환
    ```java
    public Supplier<List<T>> supplier() { 
        return ArrayList::new; 
    }
    ```
2. accumulator: 결과 컨테이너에 요소 추가
    ```java
    public BiConsumer<List<T>, T> accumulator() { 
        return List::add; 
    }
    ```
3. finisher: 최종 변환값을 결과 컨테이너로 적용
   이미 최종결과일 때 변환 과정 없이 항등 함수를 반환한다.
    ```java
    public Function<List<T>, List<T>> finisher() {
        return Function.identity();
    }
    ```
4. combiner 메서드: 두 결과 컨테이너 병합
   ```java
   public BinaryOperator<List<T>> combiner() {
    return (list1, list2) -> {
        liat.addAll(list2);
        return list1;
        }
    }
   ```
5. characteristics 메서드
   컬렉터의 연산을 정의하는 Characteristics 형식의 불변 집합 반환  
   항목으로는 `UNORDERED, CONCURRENT, IDENTITY_FINISH`가 있다.
   ```java
    public Set<Characteristics> characteristics() { 
        return Collections.unmodifiableSet(
            EnumSet.of(IDENTITY_FINISH, CONCURRENT)
        ); 
    }
   ```
6. 소수로만 나누기
    대상의 제곱보다 큰 소수를 찾으면 검사를 중단하기 위해 takeWhile을 사용할 수 있다. 자바9에 나타난 takeWhile이기 때문에 자바 8에서는 직접 구현해야 한다.
   ```java
   public boolean isPrime(List<Integer> primes, int candidate) { 
       int candidateRoot = (int) Math.sqrt((double)candidate); 
       return primes.stream() 
        .takeWhile(i -> i <= candidateRoot)
        .noneMatch(i -> candidate % i == 0); 
    }
   ```

> 6.6 커스텀 컬렉터를 구현해서 성능 개선하기는 다음에 하기

참고로 Stream에서 boxed는 IntStream을 사용한다고 가정할 때 Stream\<Integer\>로 바꿔준다.