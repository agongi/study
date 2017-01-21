## Spring Bean Life-Cycle
Interface BeanPostProcessor implementations is widely applied to all bean's creation. A bean has life-cycle of creation or destruction in following:

The life-cycle of bean is managed in spring IoC container.

> You need to use reflection to process specific bean's life-cycle in implementations of BeanPostProcessor. It receives notify from `all bean's generation`.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.03 ~
ㅁ References:
 - http://duckranger.com/2012/04/spring-mvc-dispatcherservlet
 - https://blog.outsider.ne.kr/902
 - http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html
```

### 1. Initialization
1. BeanNameAware's setBeanName
2. BeanClassLoaderAware's setBeanClassLoader
3. BeanFactoryAware's setBeanFactory
4. ResourceLoaderAware's setResourceLoader (only applicable when running in an application context)
5. ApplicationEventPublisherAware's setApplicationEventPublisher (only applicable when running in an application context)
6. MessageSourceAware's setMessageSource (only applicable when running in an application context)
7. ApplicationContextAware's setApplicationContext (only applicable when running in an application context)
8. ServletContextAware's setServletContext (only applicable when running in a web application context)
9. **postProcessBeforeInitialization** methods of `BeanPostProcessors`
10. **@PostConstruct**
11. InitializingBean's **afterPropertiesSet**
12. a custom **init-method** definition
13. **postProcessAfterInitialization** methods of `BeanPostProcessors`

> Implementation of BeanPostProcessor is notified all bean's creation.

### 2. Destruction
1. **@PreDestroy**
2. DisposableBean's **destroy**
3. a custom **destroy-method** definition

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

      container.registerShutdownHook();
  }
}
```

#### Hello.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
@Getter
@Setter
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

        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {

        log.debug("[{}] postProcessAfterInitialization()", beanName);

        return bean;
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

```
2016-09-13 03:10:10 [main] DEBUG com.games.processor.AppleProcessor - [org.springframework.context.event.internalEventListenerFactory] postProcessBeforeInitialization()
2016-09-13 03:10:10 [main] DEBUG com.games.processor.AppleProcessor - [org.springframework.context.event.internalEventListenerFactory] postProcessAfterInitialization()
2016-09-13 03:10:10 [main] DEBUG com.games.processor.AppleProcessor - [hello] postProcessBeforeInitialization()
2016-09-13 03:10:10 [main] DEBUG com.games.model.bean.Hello - [Hello.class] @PostConstruct initialized()
2016-09-13 03:10:10 [main] DEBUG com.games.model.bean.Hello - [Hello.class] InitializingBean.afterPropertiesSet()
2016-09-13 03:10:10 [main] DEBUG com.games.model.bean.Hello - [Hello.class] init-method started()
2016-09-13 03:10:10 [main] DEBUG com.games.processor.AppleProcessor - [hello] postProcessAfterInitialization()
2016-09-13 03:10:10 [Thread-0] DEBUG com.games.model.bean.Hello - [Hello.class] @PreDestroy destructed()
2016-09-13 03:10:10 [Thread-0] DEBUG com.games.model.bean.Hello - [Hello.class] DisposableBean.destroy()
2016-09-13 03:10:10 [Thread-0] DEBUG com.games.model.bean.Hello - [Hello.class] destroy-method finished()
```
