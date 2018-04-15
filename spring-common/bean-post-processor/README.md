## BeanPostProcessor
`BeanPostProcessor` that is notified when bean is instantiated **for all bean's life-cycle.**

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.mkyong.com/java/java-custom-annotations-example/
- https://deors.wordpress.com/2011/09/26/annotation-types/
- http://javadevtips.blogspot.kr/2012/12/work-with-annotations-in-spring.html
- https://dzone.com/articles/spring-annotation-processing-how-it-works
- http://www.alexecollins.com/java-annotation-processor-tutorial/
- http://snoopy81.tistory.com/171
- http://www.programcreek.com/java-api-examples/index.php?api=org.springframework.core.annotation.AnnotationUtils
- http://knight76.tistory.com/entry/spring-Spring-Utis%EC%9D%98-ReflectionUtils-%EC%82%AC%EC%9A%A9-%EC%98%88%EC%A0%9C
- http://dev-ahn.tistory.com/130
```

### BeanPostProcessor
#### Hello (== Target Class)
```java
/**
 * @author suktae.choi
 */
@Apple("hello suktae")
@Service
public class Hello implements DisposableBean, InitializingBean {
    private String message;

    // getter/setter
}
```

#### Apple (== @Interface)
```java
/**
 * @author suktae.choi
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface Apple {
    String value() default "";
}
```

#### AppleProcessor
```java
/**
 * @author suktae.choi
 */
@Slf4j
@Component
public class AppleProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        log.debug("[{}] postProcessBeforeInitialization()", beanName);

        Apple apple = bean.getClass().getAnnotation(Apple.class);

        if (apple == null) {
            return bean;
        }

        Field field = ReflectionUtils.findField(bean.getClass(), "message");

        ReflectionUtils.makeAccessible(field);
        ReflectionUtils.setField(field, bean, apple.value());

        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        log.debug("[{}] postProcessAfterInitialization()", beanName);

        return bean;
    }
}
```

#### Main
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class Application {
  public static void main(String args[]) {
      AbstractApplicationContext container = new ClassPathXmlApplicationContext("applicationContext.xml");

      Hello hello = (Hello) container.getBean("hello");
      log.debug(hello.getMessage());

      container.registerShutdownHook();
  }
}
```

### AspectJ
#### FruitAspect
```java
@AspectJ
public class FruitAspect implements InitializingBean {

    @Autowired
    private FruitProcessor processor;

    @Override
    public void afterPropertiesSet() throws Exception {
        Reflections reflections = new Reflections("com.home.service", new MethodAnnotationsScanner());
        Set<Method> targetMethods = reflections.getMethodsAnnotatedWith(Fruit.class);

        for (Method method : targetMethods) {
            Annotation[][] parameterAnnotations = method.getParameterAnnotations();
            // validation..
        }
    }

    @Before(value = "@annotation(fruit)")
    public void before(JoinPoint joinPoint, Fruit fruit) {
        String fruitKey = findFruitKey(joinPoint);
        processor.before(fruitKey);
    }

    @AfterReturning(value = "@annotation(fruit)", returning = "returnData")
    public void after(JoinPoint joinPoint, Fruit fruit, Object returnData) {
        String fruitKey = findFruitKey(joinPoint);
        List<UpdatedData> datas = processor.after(fruitKey, returnData);
    }

    private String findFruitKey(JoinPoint joinPoint) {
        Object[] obj = joinPoint.getArgs();
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        String methodName = signature.getMethod().getName();
        Class<?>[] parameterTypes = signature.getMethod().getParameterTypes();

        try {
            Annotation[][] annotations = joinPoint.getTarget().getClass().getMethod(methodName, parameterTypes).getParameterAnnotations();
            for (int i = 0; i < annotations.length; i++) {
                for (int j = 0; j < annotations[i].length; j++) {
                    if (annotations[i][j] instanceof FruitKey) {
                        Object fruitKey = obj[i];

                        return (String) fruitKey;
                    }
                }
            }
            throw new IllegalArgumentException();
        } catch (NoSuchMethodException e) {
            throw new IllegalArgumentException();
        }
    }
}
```
