---
title: "이펙티브 자바 벼락치기"
date: 2022-03-28T01:50:34+09:00
draft: false
categories:
  - java
tags:
  - java
---
# 이펙티브 자바

## Item1. 생성자 대신 정적 팩토리 메서드를 고려하라

정적 팩토리 메서드(static factory method)는 그 `클래스의 인스턴스를 반환`하는 단순한 정적 메서드이다.

```java
//생성자
Boolean a = Boolean("true")

//정적 팩토리 메서드
Boolean a= Boolean.valueOf("true");
```

아래와 같은 장점을 가질 수 있다.

1. 생성자와 달리 이름을 가질 수 있다.
2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.
3. 반환 타입의 하위 타입 객체를 반환할 수 있다.
Java8 이전에는 인스턴스에 static 메서드를 선언할 수 없어 Companion class를 만들어 거기에 정의를 했어야 했다. → 8이후부터는 `interface에 static 메서드 선언 가능`
4. 입력 매개변수 따라 매번 다른 클래스의 객체를 반환할 수 있다.
5. 코드 작성 시점에 반환할 객체의 클래스가 존재하지 않아도 된다.
ex) DriverManager.getConnection, Class.forName 때문에

단점은 아래와 같다.

1. 상속이 불가능
2. 프로그래머가 정적 팩토리 메서드를 찾기 어려워 한다.
→ 생성자처럼 설명에 명확히 드러나 있지 않아서

### 정적 팩토리 메서드 네이밍 방식

- from
    
    매개변수 하나를 받아 해당 타입의 인스턴스 반환
    
    ```java
    Date d = Date.from(instant);
    ```
    
- of
    
    여러 매개변수 받아 인스턴스 반환
    
    ```java
    Set<String> alphabets = Set.of("a", "b", "c"); 
    ```
    
- valueOf
    
    ```java
    BigInteger bigInts = BigInteger.valueOf(Integer.MAX_VALUE);
    ```
    
- instance / getInstance
    
    명시한 인스턴스를 반환하나 같은 인스턴스임을 보장하진 X
    
    ```java
    Fruit fruit = Fruit.getInstance(inputs);
    ```
    
- create / newInstance
    
    매번 새로운 인스턴스를 생성해 반환 보장
    
- getType
    
    getInstance와 같으나, 다른 클래스에 팩토리 메서드를 정의할 때 쓴다.
    
    ```java
    FileStore fs = Files.getFileStore(Path);
    ```
    
- newType
    
    newInstance와 같으나, 다른 클래스에 팩토리 메서드를 정의할 때 사용.
    
- type
    
    getType과 newType의 간결한 버전
    
    ```java
    List<Fruit> fruits = Collections.list(manyFruits);
    ```
    

## Item2. 생성자에 매개변수가 많다면 빌더를 고려하라

## Item3. private 생성자나 열거 타입으로 싱글턴임을 보증하라

클래스를 싱글톤으로 만들면, 이를 사용하는 클라이언트를 테스트하기 어려워질 수 있다. mocking하기 어렵기 때문이다. 싱글턴을 생성하는 방법은 

1. 생성자는 private으로, 

2. 1)public static 멤버를 하나 마련하여 유일하게 인스턴스에 접근하게 하거나, 

(권한이 있는 클라이언트는 리플렉션 API인 AccessibleObject.setAccessible로 private 생성자를 호출할 수 있다.)

```java
public class SingleFruit{
	public static final SingleFruit INSTANCE = new SingleFruit();
	private SingleFruit(){ ... }
	...
}
```

2)정적 팩토리 메서드를 public static 멤버로 제공하는 방법,

```java
public class SingleFruit{
	public static final SingleFruit INSTANCE = new SingleFruit();
	private SingleFruit(){ ... }
	public static SingleFruit getInstance() { return INSTANCE; }
	...
}
```

3)**원소가 하나인 열거 타입을 선언하는 방법** 3가지가 있다.

```java
public enum SingleFruit{
	INSTANCE;
	...
}
```

대부분 3번째 방법이 좋다.

## Item4. 인스턴스화를 막으려거든 private 생성자를 사용하라.

## Item5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라.

정적 유틸리티를 사용할 때,

1) 생성자를 private으로 하는 방법

2) 싱글톤 방식으로 사용하는 방법

이 2가지 방법을 많이 볼 수 있다.

사용하는 자원에 따라 동작이 달라지는 클래스에는 정적 유틸리티 클래스나 싱글턴 방식이 적합하지 않다.

ex) 특정 Dictionary 하나를 사용하는 유틸리티가 있다면 위 2가지 방법으로 사용할 수 있는데, 만약 요구사항이 들어와 String이 들어오면 StringDictionary, Int가 들어오면 IntDictionary를 리턴해야 한다고 가정하자. 이 경우 멀티 스레드 환경에서 사용할 수 없고 사용하는 자원에 따라 동작이 달라진다.

```java
public class RightDictionary{
	private final StringDictionary dictionary;
	public RightDictionary(StringDictionary Dictionary){
		this.dictionary = dictionary;
	}
	...
}
```

## Item6. 불필요한 객체 생성을 피하라.

```java
//절대 안 됨 - 문자열 인스턴스 계속 생성
String s = new String("s");

//개선 - 같은 문자열을 사용하면 같은 객체 재사용
String s = "s";
```

str.matches도 그렇다. 

성능이 중요한 상황에서 예제1처럼 반복해 사용하기보단 예제2로 사용하는 게 낫다.

```java
//예제1
static boolean isStartAbc(String str){
	return str.matches("^abc*");
}

//예제2
public class Checker{
	private static final Pattern ABC_PATTERN = Pattern.compile("^abc*");
	
	static boolean isStartAbc(String str){
		return ABC_PATTERN.matcher(str).matches();
	}
}
```

## Item7. 다 쓴 객체 참조를 해제하라

자바의 경우 가비지 컬렉터 때문에 메모리 관리에 있어 편해진다. 

하지만 메모리 누수 때문에 가비지 컬렉션 활동과 메모리 사용량이 늘어나 성능이 저하되고 심하면 OOM(OutOfMemory)으로 프로그램이 종료되기도 할 수 있다.

메모리 누수를 찾는 것이 어려운데, 객체 참조 하나를 살려두면 GC(Garbage Collector)는 모든 걸 회수해가지 못한다. 그래서 아래처럼 원소 참조가 더 이상 필요없어지면 `null 처리(참조 해제)해줄 수 있`다.

```java
public Object pop(){
	Object result = elements[--size];
	elements[size] = null;
	return result;
}
```

그럼 매번 이렇게 null로 해줘야 할까? 아니다.`null 처리 해주는 것은 예외적인 경우`여야 한다.

스택 클래스는 자기 메모리를 직접 관리하여 메모리 누수에 취약하다. 비활성 영역은 쓰이지 않는다. GC가 보기엔 비활성 영역 객체도 활성 영역과 같이 유효한 객체로 본다.

→ 자기 메모리를 직접 관리하는 클래스의 경우에 메모리 누수에 주의해야 한다.

→ `캐시` 역시 메모리 누수를 일으키는 주범이다.

: `WeakHashMap` - 엔트리가 살아 있는 캐시가 필요한 경우 사용한다. 다 쓴 엔트리는 즉시 자동으로 제거된다.

: 근데 캐시 엔트리 유효 기간을 모르니, 점점 엔트리 `가치를 떨어뜨리는 방식`을 흔히 사용한다. 쓰지않는 엔트리는 백그라운드 스레드를 활용하거나, 새 엔트리를 추가할 때 부수 작업을 수행함으로써 청소해줘야 한다.

## Item8. finalizer, cleaner 사용을 피하라

두가지 객체 소멸자 finalizer, cleaner가 있는데, 예측할 수 없고 성능에 위험할 수 있다. 보안 문제를 야기할 수 있다.

자바 라이브러리의 일부 클래스는 안전망 역할의 finalizer를 제공한다. (FileInputSystem, FileOutputSystem, ThreadpoolExecutor가 대표적이다)

## Item9. try-finally 보다는 try-with-resuorces를 사용하라.

자원 2개를 사용해야 할 때 try-finally방식을 보자.

```java
static void copy(String src, String dst) throws IOException{
	InputStream in = new FileInputStream(src);
	try{
		OutputStream out = new FileOutputStream(dst);
		try { 
				//...
				out.write(buf, 0, n)
		} finally { out.close(); }
	}finally{in.close();}
}
```

예외는 try, finally 둘다 발생할 수 있어 close 메서드가 실패할 수 있다.

또 첫번째 예외를 기록하도록 코드 수정하면 코드가 너무 지저분해진다.

이런 문제들은 try-with-resources로 모두 해결되었다.(정확하고 쉽게 자원을 회수할 수 있다)

또, 닫아야 하는 자원을 뜻하는 클래스를 작성한다면 AutoCloseable 인터페이스를 구현해야 한다.

```java
static void copy(String src, String dst) throws IOException{
	try(InputStream in = new FileInputStream(src);
		OutputStream out = new FileOutputStream(dst);){
		//...
		out.write(buf, 0, n)
	}finally{in.close();}
}
```

## Item10. equals는 일반 규약을 지켜 재정의하라

```java
@Override
public boolean equals(Object o) {
    if (o instanceof String) return s.equalsIgnoreCase((String) o);
    return false;
}
```

“Polish”.equals(”polish”)를 사용하면, false를 리턴할 것이다. 이렇게 대칭성을 위반하는 경우를 볼 수 있다.

추이성도 있다. 추이성은 1,2번째 객체가 같고, 2,3번째 객체가 같다면, 1,3번째 객체도 같아야 한다는 것이다.

예를 들어 좌표 Point의 equals()가 아래처럼 있다고 가정하자.

```java
public class Point{
	...
	@Override public boolean equals(Object o){
		if(!(o instanceof Point)) return false;
		return o.x = x && o.y = y;
	}
}
```

좌표 Point에 색을 입히고 싶어, Point를 상속한 ColorfulPoint가 나타났다.

```java
public class ColorfulPoint extends Point{
	...
	@Override public boolean equals(Object o){
		if(!(o instanceof ColorfulPoint)) return false;
		return super.equals(o) && o.color = color;
	}
}
```

아래 두 객체가 있다고 가정하자.

```java
Point p = new Point(1,2);
ColorfulPoint cp = new ColorfulPoint(1, 2, Color.BLUE);
```

p.equals(cp)는 true, cp.equals(p)는 p가 ColorfulPoint가 아니기 때문에 false가 나타날 것이다. 그럼 추이성을 깨버린다.

→ 구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다.

→ 꼭 필요한 경우가 아니라면 equals를 재정의하지 말자.

## Item11-14 재정의, Comparable

- **Item11. equals를 재정의하려거든 hashCode도 재정의하라**
    
    Object에서, equals(Object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다고 명세했다.
    
- **Item12. toString을 항상 재정의하라.**
    
    유익한 정보를 반환하며, 디버깅을 수월하게 해준다.
    
- **Item13. clone 재정의는 주의해서 진행하라**
    
    일반 클래스, 인터페이스에는 Cloneable을 구현/확장해서는 안 된다. final 클래스라면 위험이 크진 않지만, 드물게 허용해야 한다. 배열만은 clone 메서드 방식을 사용할 수 있다.(*더 알아보기*)
    
- **Item14. Comparable을 구현할지 고려하라**
    
    Comparable의 compareTo 메서드에서 <, >를 사용하는 것은 거추장스럽고 오류를 유발하여 이제는 추천하지 않는다. Comparator의 compare() 또는 비교자 생성 메서드를 사용하자.
    

## Item15-16 클래스와 멤버

- **Item15. 클래스와 멤버의 접근 권한을 최소화해라**
    
    public 가변 필드를 갖는 클래스는 일반적으로 스레드 safe하지 않다.
    
    정보 은닉으로 여러 컴포넌트를 병렬적으로 개발 가능하고, 관심사 분리, 재사용성 등의 장점이 있다.
    
- **Item16. 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라**
    
    getter/setter
    
- **Item17. 변경 가능성을 최소화하라**
    
    불변 클래스(ex)String, BigInteger, BigDecimal)는 가변 클래스보다 설계, 구현, 사용이 쉽고 오류의 여지도 적고 안전하다. 불변 객체는 스레드 safe하며 따로 동기화할 필요 없고 안심하고 공유할 수 있다.
    
    **클래스를 불변으로 만드는 5가지 규칙**
    
    : 클래스를 확장할 수 없게 한다. / 객체 상태를 변경하는 메서드를 제공하지 않는다 / 모든 필드를 final로 선언, private로 선언 / 자신 외에는 내부 가변 컴포넌트를 접근할 수 없게 한다.
    

## Item18-22 상속

- **Item18. 상속보다는 컴포지션을 사용하라.**
    
    컴포지션: 기존 클래스가 새로운 클래스의 구성 요소로 쓰인다는 뜻에서 이러한 설계를 컴포지션이라고 한다.
    
- **Item19. 상속을 고려해 설계하고 문서화하라. 그러지 않았다면 상속을 금지하라**
- **Item20. 추상 클래스보다는 인터페이스를 우선하라**
    
    다중 상속의 장점이 있고, 믹스인 정의에 안성맞춤이다.
    
    - 믹스인: 클래스가 구현할 수 있는 타입. 대상 타입의 주된 기능에 선택적 기능을 ‘혼합'한다고 해서 믹스인이라고 부른다.
    
    인터페이스와 디폴트 메서드를 사용하는 방식이 많이 사용된다. 
    
- **Item21.인터페이스는 구현하는 쪽을 생각해 설계하라**
    
    디폴트 메서드는 기존 구현체에 런타임 오류를 일으킬 수 있다.
    새로운 메서드를 추가하면 커다란 위험도 딸려 온다.
    
- **Item22. 인터페이스는 타입을 정의하는 용도로만 사용하라**
    
    ```java
    //아래 처럼 절대 사용하지 말자. 그냥 ABC;에서 끝내고 타입을 정의만 하자.
    public interface MyConstant{
    	static final double ABC = 6.4323e23;
    }
    ```
    
- **Item23. 태그 달린 클래스보다는 클래스 계층 구조를 활용하라.**
    
    Point, Rectangle, Circle을 한 클래스에 몰아넣는 것보다는, 따로 분리하여 abstract class Point, class Rectangle 처럼 계층 구조로 활용하자.