# AOP Proxy
```
https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html
https://www.baeldung.com/cglib
https://www.baeldung.com/java-dynamic-proxies
```

@Aspectj 빈을 선언했을때 및 기타 aop 기능 (ex. @Transactional) 을 어떤 방식으로 처리할지 정의

## Proxy 방식
`runtime weaving` 으로 동작하며 실제 클래스를 Proxy 로 런타임에 감싸서 aop 처리합니다

### JDK proxy
인터페이스에 Proxy 를 적용합니다
```java
@EnableAspectJAutoProxy(proxyTargetClass = false)
public class ABCConfig {
    // ...
}
```

### CGLIB
실제 클래스에 Proxy 를 적용합니다
```java
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class ABCConfig {
    // ...
}
```

## bytecode 방식
`compile weaving` 으로 동작하며 실제 클래스의 코드를 컴파일 시점에 조작해서 aop 처리합니다

### AspectJ
아래의 설정을 하면 cglib -> aspectj 로 aop 처리할 수 있습니다:
```
// src/main/resources/META-INF/aop.xml
<aspectj>
  <aspects>
    <aspect name="com.example.YourAspect"/>
  </aspects>
  <weaver options="-verbose">
    <!-- Optional: 특정 패키지만 weaving -->
    <include within="com.example..*"/>
  </weaver>
</aspectj>

implementation 'org.aspectj:aspectjweaver'

java -javaagent:/path/to/aspectjweaver.jar -jar ROOT.jar
```

