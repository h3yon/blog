# [TIL] Eureka, Zuul

<img width="648" alt="image" src="https://user-images.githubusercontent.com/46602874/207081700-9f7ebeb1-ac0e-47a5-9a43-f8aabf185874.png">

유레카는 다들 알다시피 서비스 디스커버리이며, 서버 연락처와 같다.  

## API Gateway Service, Zuul

<img width="210" alt="image" src="https://user-images.githubusercontent.com/46602874/207098620-4270ef8c-3dc8-4e82-9a77-1b7fcb76776b.png">

이렇게 하면 아래를 적용할 수 있다.

- 인증 및 권한 부여
- 서비스 검색 통합
- 응답 캐싱
- 정책, 회로 차단기 및 QoS 다시 시도
- 속도 제한
- 부하 분산
- 로깅, 추적, 상관관계 모아놓을 수 있음
- 헤더, 쿼리 문자열 및 청구 변환
- IP 허용 목록에 추가

Spring Cloud에서의 MSA간 통신은 아래와 같다.

1. RestTemplate
2. Feign Client

또, Ribbon으로 서비스 이름으로 필요한 데이터를 호출할 수 있다.  
health check로 서버가 살아있는지 알 수 있다.

### Zuul 구현 

- 2개 서비스
- 넷플릭스 zuul (gateway)







