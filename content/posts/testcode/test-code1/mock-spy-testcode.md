---
title: "테스트코드의 Mock, MockBean, Autowired, Spy 이해하기"
date: 2021-12-26T23:47:34+09:00
draft: false
categories:
  - spring
tags:
  - spring
  - test code
  - mock
  - spy
---


# Test code

+mock과 stub의 차이? 행위 검증을 추구한다는 것. mock은 행위검증을 추구하고, stub은 그렇지 않다

**1. MockBean, Mock, Autowired**

![image](https://user-images.githubusercontent.com/46602874/147401347-8235180e-abac-4ce2-a408-3bbfe4c83144.png)

target(mock)으로 보면 되겠다!
예를 들어서 controller 생성자에 service가 필요하다고 가정하면,
controller(service)이므로 controller에 @InjectMocks, service에 @Mock을 해준다

아래를 보면 알겠지만, 

MockBean/SpyBean은 Autowired로 기억됨을 알 수 있다

`@MockBean`: mock객체를 스프링 컨텍스트에 등록

`@Autowired`: @MockBean으로 등록한 mock 객체를 주입 받아서 의존성 해결

@MockBean의 의존성 주입 Target을 @InjectMocks로 하려고 하면 실패한다

**2. SpyBean**

`@SpyBean`: spy 객체를 스프링 context에 등록

`@Autowired`: @SpyBean으로 등록한 spy 객체를 주입 받아서 의존성 해결

=> SpyBean

이 인터페이스일 경우에는 해당 인터페이스를 구현하는 `실제 구현체가 스프링 컨텍스트가 등록되어 있어야` 함

**3. Spy**

실제 기능을 그대로 사용하고 싶을 때, @Spy로 spy객체 주입

ex) 예를 들어 orderService의 createOrder는

- `orderRepository.findOrderList()`를 하고 그 결과의 size가 0보다 크면 exception을 throw해준다.
- 예외가 없다면 order.Repository의 `createOrder`를 해준다.
- isNotify가 있다면 notificationClient의 `notifyToMobible`을 해준다.

이에 대해 테스트하는 코드를 보자

```java
@ExtendWith(MockitoExtension.class)
public class OrderServiceTest{
	@Spy
	private OrderRepository orderRepository;
	@Spy
	private NotificationnClient notificationClient;
	@InjectMocks
	private OrderService orderService;

	@Test
	public void createOrderTest(){
		//given
		Mockto.doAnswer(invocation -> {
			System.out.println("createOrderrr");
			return null;
		}).when(orderRepository).createOrder();
		Mockto.doAnswer(invocation -> {
			System.out.println("notifyToMobilee");
			return null;
		}).when(notificationClient).notifyToMobile();

		//when
		orderService.createOrder(true);
		//then
		Mockito.verify(orderRepository, times(1)).createOrder();
		Mockito.verify(notificationCliennt, times(1)).notifyToMobile();
	}
```

보면 테스트 코드에 createOrder, notifyToMobile이 호출되면 할 부분은 명시되어 있는데,

findOrderList()는 보이지 않는다. 이러면 에러가 나타나야 하지만, 

Spy로 되어 있기 때문에 기존기능을 사용함을 알 수 있다. 

Spy는 이처럼 실제 기능을 사용하고 싶을 때 사용한다.

출처
[https://cobbybb.tistory.com/16](https://cobbybb.tistory.com/16)