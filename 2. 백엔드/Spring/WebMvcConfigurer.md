# WebMvcConfigurer
Spring MVC를 초기화 하는데 사용됨.  
Spring MVC는 기본적으로 알맞게 동작하도록 많은 기본 설정을 제공하지만, 인터셉터 추가, 리소스 핸들러 등록, 포매터 설정, 뷰 리졸버 등을 구성하려면 WebMvcConfigurer를 구현하여야 한다. 

# 설정 방법
```
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@EnableWebMvc // Spring MVC 자동 구성 비활성화
public abstract class CustomWebMvcConfigurer implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry)
    ...(이하 생략)...
}
```

보통은 위와 같이 구현체를 하나 만들어놓고 아래 클래스를 사용한다. 

```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;

@Configuration
public class WebMvcConfig extends CustomWebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        super.addInterceptors(registry);
    }
}
```

# WebMvcConfigurer vs servlet-context.xml
servlet-context.xml을 사용하면 코드가 깔끔해진다는 장점이 있다.   
하지만 servlet-context.xml에서는 정적인 설정만 가능하며, 런타임 중에 설정 변경이 불가능하다. (서버 재시작 필요)