# Spring Cloud에서의 MSA간 통신
- RestTemplate
- Feign Client

# RestTemplate
일반적으로 MSA에서 많이 쓰이는 API 통신 방법
```java
RestTemplate restTemplate = new RestTemplate();
restTemplate.getForObject("http://localhost:8080/", User.class, 200);
```

# Feign Client
마이크로 서비스 이름으로 호출 가능
```java
@FeignClient("stores")
public interface StoreClient {
    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    List<Store> getStores();
}
```