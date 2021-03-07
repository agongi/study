## BeanPostProcessor vs BeanFactoryPostProcessor

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.mkyong.com/java/java-custom-annotations-example/
- http://wonwoo.ml/index.php/post/899
```

## BeanPostProcessor

- 각 bean 생성될때마다 호출 (즉 bean 의 개수만큼 호출됨)
- 생성된 빈의 afterPropertiesSet 전/후 hooks

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

## BeanFactoryPostProcessor

- BeanFactory 는 ApplicationContext 의 부모로써, 1번만 호출됨
- (아직 생성안된) bean 의 metadata 를 변경하기 위해 사용됨

## AspectJ

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
