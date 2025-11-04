# Spring AOP
```
https://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html
https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
https://www.mkyong.com/spring/spring-aop-examples-advice/
```

- Advice - **when**
  - @Before - Before the method execution
  - @AfterReturning - After the method returned a result, intercept the returned result as well.
  - @AfterThrowing - After the method throws an exception
  - @After (== finally) - After the method is invoked (a.k.a after the afterReturning and/or afterThrowing)
  - @Around - Run around the method execution, combine all three advices above.
- Aspect - **what**
- PointCut - **who**
  - execution
  - within
  - this
  - target
  - args
  - @annotation

```java
/**
 * enable aspectJ
 */
@Configuration
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AspectJConfiguration {
  // ...
}

@AspectJ
@Component
public class DefaultRestAspect {
  @Around(value = "@annotation(repayable) && execution(* com.toy.controller.*.*(..))")
  public Object doBasicProfiling(ProceedingJoinPoint joinPoint) throws Throwable {
    Object response = joinPoint.proceed();
    // do something
    
    return response;
  }
}
```

## Proxy 방식
`runtime weaving` 으로 동작하며 실제 클래스를 Proxy 로 런타임에 감싸서 aop 처리합니다

### JDK
인터페이스에 Proxy 를 적용합니다:
```java
// proxyTargetClass=true → JDK 기반 프록시
@EnableAspectJAutoProxy(proxyTargetClass = false)
public class AopConfig {
    // ...
}
```

### CGLIB
실제 클래스에 Proxy 를 적용합니다:
```java
// proxyTargetClass=true → CGLIB 기반 프록시
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class AopConfig {
    // ...
}
```

## bytecode 방식
`compile weaving` 으로 동작하며 실제 클래스의 코드를 컴파일 시점에 조작해서 aop 처리합니다

### AspectJ
classpath 에 `aspectjweaver` 의존이 있다면 사용할 수 있습니다:
```java
// proxyTargetClass=true -> CGLIB 기반 프록시 강제
// exposeProxy=true -> 내부 self-invocation 시 AOP 적용 가능
@EnableAspectJAutoProxy(proxyTargetClass = true, exposeProxy = true)
public class AopConfig {
    // ...
}
```
```gradle
implementation 'org.springframework.boot:spring-boot-starter-aop'
implementation 'org.aspectj:aspectjweaver'
```