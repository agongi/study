## Spring AOP

```
ㅁ Author: suktae.choi
- https://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html
- https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
- https://www.mkyong.com/spring/spring-aop-examples-advice/
```

#### Index

- [AOP Proxy](aop-proxy)
- [ProxyFactoryBean](proxy-factory-bean)

***

### 기본개념

**Advice** the **Aspect** to **PointCut** target.

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
@EnableAspectJAutoProxy
public class AspectJConfiguration {
  // ...
}

@AspectJ
@Component
public class DefaultRestAspect {
  @Before(value = "@annotation(repayable) && execution(* com.toy.controller.*.*(..))")
  public void before() {
    // aspect executed before controller
  }

  @AfterReturning(value = "@annotation(repayable) && execution(* com.toy.controller.*.*(..))", returning = "returnVal")
  public void afterReturning(JoinPoint joinPoint, Object returnVal) {
    // aspect executed after controller's response is successfully returned
  }

  @Around(value = "@annotation(repayable) && execution(* com.toy.controller.*.*(..))")
  public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
    // @Before
    Object returnVal = pjp.proceed();
    // @AfterReturning
    return returnVal;
  }
}
```

### Self Invacation

서비스에서 정말 필요한 경우가 있는데, 아래처럼 사용 가능하다.

**AopContext**

```java
@EnableAspectJAutoProxy(exposeProxy = true)
public class AopConfig {
  // ...
}

public static void main(String[] args) {
	Object proxy = AopContext.currentProxy();
}
```

exposeProxy=true 가 설정되면 ThreadLocal 에 현재 proxy 가 저장되서, context 를 통해 가져올 수 있다.

**Self inject**

```java
@Service
public class UserService {
  @Autowire
  private UserService userService;
  
  public String getName() {
  	return userService.selectNameById(1L);
  }
  
  @Transactional
  public String selectNameById(Long id) {
    return "test-name"; // select
  }
}
```

[spring 4.3 부터](https://github.com/spring-projects/spring-framework/commit/4a0fa69ce469cae2e8c8a1a45f0b43f74a74481d) self inject 가 지원되므로, 사용가능하다

**AspectJ**

compile 단계에서 bytecode instrument 로 코드주입이 되므로, 별다른 처리 없이 self invocation 이 사용 가능하다.

