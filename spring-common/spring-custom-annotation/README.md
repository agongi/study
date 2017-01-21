## Spring Custom Annotation
Spring fundamental of Annotation.

> Get annotation @Apple if it exists, and fetch meta-data from it to refresh class field even if it is scope private using reflection.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.11
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

The main is simple. It just loads `applicationContext.xml` which defines bean and extract bean name of `hello`.

ApplicationContext.xml defines bean name of hello and set value `message` as **hello world** using setter method.

Class Hello is using custom annotation name `@Apple` with values **hello suktae**.

Interface Apple is plain custom-annotation declaration.

Class AppleProcessor is the key of this section. It implements interface `BeanPostProcessor` that is notified when bean is instantiated **for all bean's life-cycle**. It uses spring **reflection** to only let class that has annotation type of Apple fetch meta-data of annotation and set it as local variable using setter.


#### Application.java
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

#### applicationContext.xml
```xml
<beans>
  <context:component-scan base-package="com.games"
                          annotation-config="true"/>

  <bean id="appleProcessor" class="com.games.processor.AppleProcessor" />

  <bean id="hello"
        class="com.games.model.bean.Hello"
        init-method="started"
        destroy-method="finished">

      <property name="message" value="hello world" />
  </bean>
</beans>
```

#### Hello.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
@Getter
@Setter
@Apple("hello suktae")
public class Hello implements DisposableBean, InitializingBean {

    String message;


    @Override
    public void destroy() throws Exception {
        log.debug("[Hello.class] DisposableBean.destroy()");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        log.debug("[Hello.class] InitializingBean.afterPropertiesSet()");
    }

    public void started() {
        log.debug("[Hello.class] init-method started()");
    }

    public void finished() {
        log.debug("[Hello.class] destroy-method finished()");
    }

    @PostConstruct
    public void initialized() {
        log.debug("[Hello.class] @PostConstruct initialized()");
    }

    @PreDestroy
    public void destructed() {
        log.debug("[Hello.class] @PreDestroy destructed()");
    }
}
```

#### Apple.java
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

#### AppleProcessor.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
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

The result of log.debug(hello.getMessage()) is **hello suktae** not hello world that is defined in applicationContext.xml. New message is injected in instantiation-time using reflection in AppleProcessor.

```
2016-09-13 02:54:34 [main] DEBUG com.games.processor.AppleProcessor - [org.springframework.context.event.internalEventListenerProcessor] postProcessBeforeInitialization()
2016-09-13 02:54:34 [main] DEBUG com.games.processor.AppleProcessor - [org.springframework.context.event.internalEventListenerProcessor] postProcessAfterInitialization()
2016-09-13 02:54:34 [main] DEBUG com.games.processor.AppleProcessor - [org.springframework.context.event.internalEventListenerFactory] postProcessBeforeInitialization()
2016-09-13 02:54:34 [main] DEBUG com.games.processor.AppleProcessor - [org.springframework.context.event.internalEventListenerFactory] postProcessAfterInitialization()
2016-09-13 02:54:34 [main] DEBUG com.games.processor.AppleProcessor - [hello] postProcessBeforeInitialization()
2016-09-13 02:54:34 [main] DEBUG com.games.model.bean.Hello - [Hello.class] @PostConstruct initialized()
2016-09-13 02:54:34 [main] DEBUG com.games.model.bean.Hello - [Hello.class] InitializingBean.afterPropertiesSet()
2016-09-13 02:54:34 [main] DEBUG com.games.model.bean.Hello - [Hello.class] init-method started()
2016-09-13 02:54:34 [main] DEBUG com.games.processor.AppleProcessor - [hello] postProcessAfterInitialization()
2016-09-13 02:54:34 [main] DEBUG com.games.Application - hello suktae
2016-09-13 02:54:34 [Thread-0] DEBUG com.games.model.bean.Hello - [Hello.class] @PreDestroy destructed()
2016-09-13 02:54:34 [Thread-0] DEBUG com.games.model.bean.Hello - [Hello.class] DisposableBean.destroy()
2016-09-13 02:54:34 [Thread-0] DEBUG com.games.model.bean.Hello - [Hello.class] destroy-method finished()
```
