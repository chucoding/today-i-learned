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
1. Feign Client인터페이스가 있는 client 패키지를 DEBUG 레벨로 설정
```yaml
logging:
  level:
    com.example.userservice.client: DEBUG
```

2. Application.java
다음과 같이 feign 패키지에 속한 Logger를 반환값으로 빈 등록
```java
@Bean
public Logger.Level feignLoggerLevel() {
    return Logger.Level.FULL;
}
```

3. 재시작 후 콘솔에서 다음과 같이 Feign client로 통신하는 Log 확인 가능
```
[OrderServiceClient#getOrders] ---> GET http://order-service/order-service/3b20b817-6b98-4995-8354-307f93095dfd/orders_ng HTTP/1.1
[OrderServiceClient#getOrders] ---> END HTTP (0-byte body)
[OrderServiceClient#getOrders] <--- HTTP/1.1 404 (187ms)
```

4. 예외처리 방법
```java
List<ResponseOrder> ordersList = null;
try {
    ordersList = orderServiceClient.getOrders(userId);
} catch (FeignException ex) {
    log.error(ex.getMessage());
}
```

### ErrorDecoder를 사용한 예외처리
일일이 try-catch로 예외처리 안해도 되고 status 별 오류메시지 전송이 가능하다는 장점이 있음.

아래와 같이 error패키지를 만들고 ErrorDecoder를 구현하는 FeignErrorDecoder 클래스 생성
```java
@Component // Autowired 사용하려면 Component로 등록해야함.
public class FeignErrorDecoder implements ErrorDecoder {

    Environment env;

    @Autowired 
    public FeignErrorDecoder(Environment env) {
        this.env = env;
    }

    @Override
    public Exception decode(String methodKey, Response response) {
        switch (response.status()) {
            case 400:
                break;
            case 404:
                if (methodKey.contains("getOrders")) {
                    return new ResponseStatusException(HttpStatus.valueOf(response.status()),
                            env.getProperty("order-service.exception.orders_is_empty"));
                }
                break;
            default:
                return new Exception(response.reason());
        }
        return null;
    }
}
```