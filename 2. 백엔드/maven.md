# Maven
자바 프로젝트를 위한 통합 빌드 툴 (Build Tool)

# Apache Maven Enforcer Plugin
 Maven 빌드 프로세스 중에 다양한 규칙을 강제로 적용하여 빌드의 일관성과 품질을 유지하도록 도와주는 플러그인  
 - JDK 버전 검사 : 프로젝트가 요구하는 JDK 버전을 확인하고, 빌드가 올바른 JDK 버전에서 실행되는지 확인
 - 의존성 버전 검사 : 특정한 라이브러리나 의존성의 특정 버전을 사용하도록 강제
 - 코드 품질 검사 : 코드 품질 툴(ex. SonarQube)을 통한 정적 분석, 코드 컨벤션을 준수하는지 검사
 - 보안 검사 : 보안 취약점 스캔 도구를 사용하여 프로젝트를 검사하고 보안 문제가 있는 경우 빌드를 중지

### 설정 방법

 ```
 <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <version>1.4.1</version>
    <executions>
        <execution>
            <id>enforce-bytecode-version</id> 
            <goals>
                <goal>enforce</goal>
            </goals>
            <configuration>
                <rules>
                    <enforceBytecodeVersion> <!--클래스 파일의 바이트 코드 버전을 확인하는 Rule-->
                        <maxJdkVersion>${jdk-version}</maxJdkVersion> <!-- jdk 버전 필수>
                        <excludes>
                            <exclude>org.mindrot:jbcrypt</exclude>
                            <exclude>org.codehaus.groovy</exclude>
                            <exclude>org.apache.logging.log4j</exclude>
                            <exclude>com.fasterxml.jackson.core</exclude>
                        </excludes>
                    </enforceBytecodeVersion>
                </rules>
                <fail>true</fail>
            </configuration>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>extra-enforcer-rules</artifactId>
            <version>1.0-beta-6</version>
        </dependency>
    </dependencies>
</plugin>
 ```

💡 exclude 설정 이유  
jackson.core 라이브러리 같은 경우 2.12 버전부터 Java 8로 빌드됨.(바이트 코드 버전)  
jdk 1.7로 빌드시 버전이 너무 낮아서 오류발생(maxJdkVersion을 1.9로 설정시 빌드 성공)  
https://www.mojohaus.org/extra-enforcer-rules/enforceBytecodeVersion.html