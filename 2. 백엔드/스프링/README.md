# Spring


### 개발시 유용한 라이브러리 및 플러그인
서버를 재기동하지 않아도 자동으로 수정사항들을 반영해주는 스프링 부트 도구
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

시스템에 설치된 자바 버전을 찾아서 빌드해주는 도구(JAVA_HOME 환경변수를 변경하지 않고자 할 때 유용)
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>${java.version}</source>
        <target>${java.version}</target>
    </configuration>
</plugin>
```