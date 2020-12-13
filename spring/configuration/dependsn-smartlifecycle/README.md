## @DependsOn - SmartLifeCycle

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.baeldung.com/spring-depends-on
```

We can choose either the **SmartLifeCycle** interface or the **@DependsOn** annotation for managing initialization order

## @DependsOn

```java
@Configuration
public class DemoConfig {
  @Bean
  public Object beanA() {
    return new BeanA();
  }

  @Bean
  @DependsOn("beanA")
  public Object beanB() {
    return new BeanB();
  }
}
```

## SmartLifeCycle

```java
@Component
public class DemoCycleConfigurer implements SmartLifecycle {
  private boolean isRunning = false;
  private Collection<Object> dependableBeans;

  @Override
  public void start() {
    // InitializingBean
    dependableBeans.forEach(o -> o.init());
  }

  @Override
  public void stop() {
    // DisposableBean
    dependableBeans.forEach(o -> o.destroy());
  }

  @Override
  public boolean isRunning() {
    return isRunning;
  }

  @Override
  public int getPhase() {
    // priority before and/or after
    return -1;
  }
}
```

