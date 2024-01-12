# Spring Cloud
분산 시스템에 빠르게 어플리케이션을 개발하는 데 목적을 두고 만들어진 라이브러리

# 프로젝트 구성 요소
- Spring Cloud Config Server (환경 설정 관리 : 다양한 환경설정 정보를 한곳에서 관리 가능, 유지보수가 월등히 쉬워진다)
- Naming Server (Eureka) : 서비스 등록, 위치정보 확인, 검색 
- Ribbon (Client Side), Spring Cloud Gateway : 서버에 들어왔던 요청 정보를 분산하기 위한 용도
- RestTemplate, FeignClient : 각각의 마이크로 서비스 간의 통신을 위해서 사용
- Zipkin Distributed Tracing, Netflix API gateway, ELK : 시각화 모니터링, 로그 추적
- Hystrix : 장애 발생시 빠르게 복구하기 위한 회복성 패턴 구현