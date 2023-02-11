---
title: "스프링 클라우드 openfeign 파악하기"
date: 2023-02-11T11:50:34+09:00
draft: false
categories:
  - java
tags:
  - java
---


개발을 하면서 외부 요청을 날릴 때 보통은 restTemplate(이제는 webclient)만 주로 사용했었다. 

비동기 프로그래밍 방식을 주로 다루었어서 webclient를 많이 사용했었다.

이번에 동기 프로그래밍 방식을 주로 다루게 되어서 외부 요청을 날릴 때 어떤 걸 주로 사용하는지 확인해봤다. 동기 프로그래밍의 경우 OpenFeign을 주로 사용한다는 걸 알게 됐다.

그외 retrofit 도 주로 사용되는 부분을 보았는데, 서버 사이드 쪽에서는 openFeign이 잘 사용된다고 해서 openFeign을 공부해보기로 했다.

openFeignClient를 사용해보자!

출처

- [https://www.baeldung.com/spring-cloud-openfeign](https://www.baeldung.com/spring-cloud-openfeign)

# 환경설정

dependency는 아래와 같다.

```jsx
implementation 'org.springframework.cloud:spring-cloud-starter-openfeign:3.1.3'
```

사용하려면 mainApplication 위에 어노테이션 하나만 붙여주면 된다.

```java
@EnableFeignClients
@SpringBootApplication
public class ExampleApplication {
    public static void main(String[] args) {
        SpringApplication.run(ExampleApplication.class, args);
    }
}
```

@EnableFeignClients 대신, 별도의 config 파일을 만들어줄 수 있다.

```java
@Configuration
@EnableFeignClients("com.디렉토리.경로")
class OpenFeignConfig {
}
```

config 파일을 만드는 것도 application.yml에 간단하게 명시해주는 것도 가능하다.

```yaml
feign:
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 5000
        loggerLevel: basic
```

만약 실패하는 테스트를 잠시 하고 싶다면
위의 `readTimeout` 을 잠시 1로 바꾸면 되겠다ㅎㅎ

# RequestInterceptor 주입

```java
@Bean
public RequestInterceptor requestInterceptor() {
  return requestTemplate -> {
      requestTemplate.header("user", username);
      requestTemplate.header("password", password);
      requestTemplate.header("Accept", ContentType.APPLICATION_JSON.getMimeType());
  };
}
```

만약 위의 requestInterceptor을 주입하고 싶을 때 아래처럼

yml에 세팅해줄 수 있다.

```yaml
feign:
  client:
    config:
      default:
        requestInterceptors:
          com.baeldung.cloud.openfeign.JSONPlaceHolderInterceptor
```

# 사용 방법

이렇게 간단하게 사용할 수 있다.

사용 예시를 보았을 때 아래와 같이 사용할 수 있고,

```java
@FeignClient(name="웹사이트", url="http://웹사이트.com")
public interface 웹사이트Client{

	@GetMapping
  웹사이트DTO get웹사이트정보(@RequestParam Int siteNo);
}
```

TODO) GetMapping과 RequestMapping 중 주로 어떤 걸 사용하는지 한번 봐봐야 할 것 같다.

```java
@FeignClient(name="웹사이트", url="http://웹사이트.com")
public interface WebsiteClient{

	@RequestMapping(method=RequestMethod.GET, value='/post')
  WebsiteDTO getWebsite(@RequestParam int siteNo);
}
```

# Hystrix

feign은 hystrix를 지원하는데, 이걸 enable하게 한다면 fallback 패턴을 주입할 수 있다.

yml 파일에 `feign.hystrix.enabled=true` 을 해준다.

fallback 방식 사용되는 부분 2가지가 유사해보이긴 한데 좀 달라보이긴 해서 정리를 해보았다.

1. implement WebsiteClient
    
    ```java
    @Sl4j
    @Component 
    public class WebsiteClientFallback implements WebsiteClient {
    
        @Override
        public WebsiteDTO getWebsite(int siteNo) {
            log.error("웹사이트 정보 가져오는 거 실패했어요!");
            return new WebsiteDTO();
        }
    }
    ```
    
2. implement FallbackFactory<WebsiteClient>
    
    ```java
    @Sl4j
    @Component 
    public class WebsiteClientFallback implements FallbackFactory<WebsiteClient> {
    
	@Override
	public WebsiteClient create(){
		return new WebsiteClient(){
		    @Override
		    public WebsiteDTO getWebsite(int siteNo) {
			log.error("웹사이트 정보 가져오는 거 실패했어요!");
			return new WebsiteDTO();
		    }	
		}
	}
    }
    ```
    

만약 위와 같은 fallback 클래스를 잘 만들어주었다면,
client 어노테이션에 fallback 내용을 추가해주면 된다.

```java
@FeignClient(name="웹사이트", url="http://웹사이트.com", fallback=WebsiteClientFallback.class)
public interface WebsiteClient{

	@RequestMapping(method=RequestMethod.GET, value='/post')
  WebsiteDTO getWebsite(@RequestParam int siteNo);
}
```

# 에러 핸들링

feign에서 에러를 던질 때 FeignException이 던져진다.

우리는 원하는 exception을 던지도록 `CustomErrorDecoder` 를 사용할 수 있다.

```java
public class CustomErrorDecoder implements ErrorDecoder {
    @Override
    public Exception decode(String methodKey, Response response) {

        switch (response.status()){
            case 400:
                return new BadRequestException();
            case 404:
                return new NotFoundException();
            default:
                return new Exception("Generic error");
        }
    }
}
```

이걸 Configuration class 에 빈으로 만들어줄 수 있다.

```java
public class ClientConfiguration {

    @Bean
    public ErrorDecoder errorDecoder() {
        return new CustomErrorDecoder();
    }
}
```

위에 정리해놨던 부분은 거의 다 알고 있을 거라고 생각이 된다.

내가 처음부터 궁금했던 부분은 아래와 같다.

```java
compile('org.springframework.cloud:spring-cloud-starter-openfeign')
compile('org.springframework.cloud:spring-cloud-starter-netflix-eureka-client')
compile('org.springframework.cloud:spring-cloud-starter-loadbalancer')
compile('org.springframework.cloud:spring-cloud-starter-netflix-hystrix')
```

openfeign을 사용할 때 위와 같은 dependency를 자주 볼 수 있다.

이들의 연관성과 상황별로 꼭 사용해야하는 dependency가 궁금하다.

이번에 이 부분을 좀 알아보면 될 것 같다.
