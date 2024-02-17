# Spring Cloud에서의 MSA간 통신
- RestTemplate
- Feign Client

# RestTemplate
일반적으로 MSA에서 많이 쓰이는 API 통신 방법
```java
RestTemplate restTemplate = new RestTemplate();
restTemplate.getForObject("http://localhost:8080/", User.class, 200);
```

일반적으로는 SpringBoot Application.java에 다음과 같이 RestTemplate을 Bean으로 등록
```java
@Bean
@LoadBalanced
public RestTemplate getRestTemplate() {
    return new RestTemplate();
}
```

아래와 같이 사용하고자 하는 클래스에서 다음과 같이 사용가능
```java
//@Autowired
//생성자 this.restTemplate = restTemplate;
String orderUrl = String.format(env.getProperty("order-service.url"), userId); // application.yaml 파일에서 url에 %s를 userId로 치환
ResponseEntity<List<ResponseOrder>> orderListResponse = restTemplate.exchange(orderUrl, HttpMethod.GET, null,
    new ParameterizedTypeReference<List<ResponseOrder>>() {});
```

# Feign Client
REST Call을 추상화 한 Spring Cloud Netflix 라이브러리  
💡 단, 유레카에 등록된 경우에만 사용가능
- 장점 : 개발자 입장에서 RestTemplate보다 조금 더 직관적인 방법(일반적인 메소드 호출하듯이 사용 가능)
- 단점 : 직접 개발한 사람이 아니면 제대로 파악이 안되는 경우도 있음.

### 셋팅방법
1. pom.xml
```xml
<!-- Feign Client -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

2. Application.java
```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
public class UserServiceApplication { ... }
```

3. client 패키지 만들고 OrderServiceClient.java 생성 (interface)
```java
@FeignClient(name="order-service")
public interface OrderServiceClient {

    @GetMapping("/order-service/{userId}/orders")
    List<ResponseOrder> getOrders(@PathVariable String userId);
}
```

4. 사용하는 곳에서 OrderServiceClient 생성자 주입 후 호출해서 사용

### 로그 추적 및 예외처리