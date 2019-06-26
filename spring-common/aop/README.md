## Spring AOP

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.credera.com/blog/technology-insights/open-source-technology-insights/aspect-oriented-programming-in-spring-boot-part-2-spring-jdk-proxies-vs-cglib-vs-aspectj/
- https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
- http://www.mkyong.com/spring/spring-aop-examples-advice/
- http://haviyj.tistory.com/33
- https://docs.spring.io/spring/docs/current/spring-framework-reference/html/aop.html
```

### Spring AOP
**Advice** to **PointCut target.**

```java
@Before(value = "@annotation(repayable)")
public void before(JoinPoint joinPoint, Repayable repayable) {
  // omitted ...
}
```

- @Before - Run before the method execution
- @After - Run after the method returned a result
- @AfterReturning - Run after the method returned a result, intercept the returned result as well.
- @AfterThrowing - Run after the method throws an exception
- @Around - Run around the method execution, combine all three advices above.

```java
@Around
public void around(ProceedingJoinPoint jointPoint) {
  // before

  Object return = jointPoint.proceed();

  // after
}
```

Spring AOP is supported in following way:
- [Based-on Proxy](spring-aop#proxy)
- [Based-on AspectJ - not using proxy](spring-aop#aspectj)

#### Proxy
- Runtime weaving
- Limitations
  - Self-invocation doesn't work around because It will not come through proxy
  - Public method only affected
  - Method should not be final
    - Proxy override target method to delegate but final keyword can't be make it

<img src="images/aop-proxy-call.png" width="75%">

##### JVM Dynamic Proxy
- Spring AOP default
- Works in `interface`

<img src="images/Picture2-4.png" width="75%">

##### CGLIB Proxy
- Spring also supports it when this one is declared
- Works in concrete `target class`

<img src="images/Picture3-3.png" width="75%">

#### AspectJ
- JVM loadtime weaving using bytecode instrument
- No limitations

<img src="images/Picture5-2.png" width="75%">

```xml
  <aop:aspectj-autoproxy proxy-target-class="true"/>

  <!-- bean define explicitly or
       using component-scan with <context:include-filter> -->
	<bean id="logAspect" class="com.aspect.LoggingAspect" />
```

```java
@Aspect
public class LoggingAspect {

	@Before("execution(* com.repository.add(..))")
	public void logBefore(JoinPoint joinPoint) {
		System.out.println("before : " + joinPoint.getSignature().getName());
	}
}
```

### AOP vs Interceptor
- AOP - postHandle()/afterCompletion are not invoked when **exception thrown in target method**
- Interceptor - @AfterThrowing/@After/@Around can handle it