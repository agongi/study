## Spring AOP

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
- https://www.mkyong.com/spring/spring-aop-examples-advice/
- http://haviyj.tistory.com/33
- https://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html
- https://whiteship.tistory.com/1284
```

#### Index

- [AOP vs Interceptor](aop-interceptor)
- [AspectJ](aspectj)
- [@Configurable](configurable)
- [Proxy](proxy)

**Advice** the **Aspect** to **PointCut** target.

- Advice - Inject when
- Aspect - contents
- PointCut - target.

Here is brief example:

```java
/**
 * enable aspectJ
 */
@Configuration
@EnableAspectJAutoProxy
public class AspectJConfiguration {
  // ...
}

/**
 * use it
 */
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

> @Aspect should be spring-bean

#### PointCut

- execution
- within
- this
- target
- args
- @annotation

#### Advice

- @Before - Before the method execution
- @AfterReturning - After the method returned a result, intercept the returned result as well.
- @AfterThrowing - After the method throws an exception
  - @After (== finally) - After the method is invoked (a.k.a after the afterReturning and/or afterThrowing)
- @Around - Run around the method execution, combine all three advices above.

#### Aspect

The contents what to do

### Order

If you need order to being executed among aspects, you can define order as following:

```java
public class AspectA implements Ordered {
  public int getOrder() {
    return 0;
  }

  // .. something
}

@Order(1)
public class AspectB {
  // .. something
}
```

> lower value has high priority.

### Reusable

PointCut expression can be resued:

```java
@Aspect
@Component
public class AspectTest {

  @Pointcut("execution(* *.*(...))")
  private void loggingAdvice() {
    // ...
  }
  
  @Around("loggingAdvice()")
  public Object loggingAround(ProceedingJoinPoint joinPoint) {
    // ...
  }
}
```

### Weaving

- Compile-time weaving : ajc를 이용하여 소스를 컴파일할 때 Weaving 작업을 진행하는 것을 의미한다.
- Post-compile weaving : 이미 컴파일된 바이너리 클래스를 Weaving하는 것을 의미한다.
- Load-time weaving (LTW) : Class Loader가 클래스를 Loading할 때 Weaving 작업을 진행한다. Weaving Agent 가 필요