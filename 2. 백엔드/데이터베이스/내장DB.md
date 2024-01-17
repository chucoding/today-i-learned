# H2 데이터베이스
Spring Boot에서 사용할 수 있는 데이터베이스

pom.xml
```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>1.4.200</version>
    <scope>runtime</scope>
</dependency>
```

application.yml
```yml
spring:
  application:
    name: user-service
  h2:
    console:
      enabled: true
      settings:
        web-allow-others: true
      path: /h2-console
  datasource:
    driver-class-name: org.h2.Driver
    url: jdbc:h2:mem:testdb
```
h2 콘솔을 외부에서 /h2-console 엔드포인트로 접속할 수 있게 설정