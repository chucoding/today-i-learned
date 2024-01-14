# Spring Cloud
분산 시스템에 빠르게 어플리케이션을 개발하는 데 목적을 두고 만들어진 라이브러리

### K8S vs Spring Cloud
- K8S
    - 자바 뿐만 아니라 다양한 언어를 지원(모든 언어에 대해 보편적인 방식으로 분산 컴퓨팅 문제를 해결)
    - 다양한 기능들을 어플리케이션 Stack이 아닌 플랫폼 레벨에서 제공
- Spring Cloud
    - 스프링 클라우드의 라이브러리들은 어플리케이션 Stack의 일부분으로써 기능을 제공(자바 개발자 친화적)
![image](https://github.com/chucoding/today-i-learned/assets/56211193/fe627c5e-5ee9-4895-9cd6-6123f13fef63)

# 프로젝트 구성 요소
Spring boot 2.4부터 대체되는 서비스가 많아지므로 주의필요.
- Spring Cloud Config Server (환경 설정 관리 : 다양한 환경설정 정보를 한곳에서 관리 가능, 유지보수가 월등히 쉬워진다)
- Naming Server (Eureka) : 서비스 등록, 위치정보 확인, 검색
- Spring Cloud Loadbalancer : 서버에 들어왔던 요청 정보를 분산하기 위한 용도(Ribbon 대체)
- Spring Cloud Gateway : 서버에 들어왔던 요청 정보를 마이크로 서비스로 전달하기 위한 용도(Zuul 대체)
- RestTemplate, FeignClient : 각각의 마이크로 서비스 간의 통신을 위해서 사용
- Zipkin Distributed Tracing, Netflix API gateway, ELK : 시각화 모니터링, 로그 추적
- Resilience4 : 장애 발생시 빠르게 복구하기 위한 회복성 패턴 구현 (Hystrix 대체)


# 참고자료
|TITLE|URL|
|---|---|
|K8S vs Spring Cloud|https://velog.io/@mdev97/Project-Spring-Cloud-vs-Kubernetes|