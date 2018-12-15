## AspectJ

```
ㅁ Author: suktae.choi
ㅁ References:
- http://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte:fdl:aop:aspectj
```

### PointCut
<img src="images/Screen%20Shot%202018-12-15%20at%2018.32.19.png" width="75%">

### Advice
#### Normal
- @Before
- @Around
- Class#method()
- `@Around`
- @After: finally
- **@AfterReturning**

#### Exception
- @Before
- @Around
- Class#method()
- @After: finally
- **@AfterThrowing**

### Code
```java
/**
 * annotation
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyAnnotation {
    TestType type();
}

/**
 * usage
 */
public class MyController {
  @RequestMapping()
  @MyAnnotation(type = TestType.TYPE_A)
  public void doSomething() {
    // ...
  }
}

/**
 * aspect
 */
@Slf4j
@Aspect
public class TestAspect {
    @AfterReturning(pointcut = "@annotation(com.my.game.MyAnnotation) && (execution(* com.my.game..*Controller.*(..)))", returning = "returnObject")
    public void afterReturning(JoinPoint joinPoint, Object returnObject) {
        // NPE
        if (returnObject == null) {
            return;
        }

        final MethodSignature methodSignature = (MethodSignature)joinPoint.getSignature();
        final Method method = methodSignature.getMethod();

        MyAnnotation annotation = AnnotationUtils.findAnnotation(method, MyAnnotation.class);

        // annotation 에 있는 값을 가져옴
        TestType type = annotation.type();

        // do something
    }
}
```
