# @Configuration
```
https://jaehun2841.github.io/2019/12/22/2019-12-22-spring-import/#%EB%A6%AC%ED%8C%A9%ED%86%A0%EB%A7%81-4-importselector%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%84%A0%ED%83%9D%EC%A0%81-configuration-%EC%82%AC%EC%9A%A9
https://www.baeldung.com/spring-import-annotation
https://www.baeldung.com/spring-depends-on
```

@Configuration 은 스프링 빈 이면서 `@Bean 어노테이션으로 빈을 생성하거나, 다른 @Configuration 클래스를 로드`하는 역할을 합니다

## Import
### ImportSelector
Enable~ 에서 import 할 @Configuration class 자체를 선택 할때 사용한다.

```java
@Import(CustomImportSelector.class)
public @interface EnableCustomFunc {
  String value();
}
```
```java
public class CustomImportSelector implements ImportSelector {
  @Override
  public String[] selectImports(AnnotationMetadata importingClassMetadata) {
    Map<String, Object> attrMap = importingClassMetadata.getAnnotationAttributes(EnableCustomFunc.class.getName());
    AnnotationAttributes attributes = AnnotationAttributes.fromMap(attrMap);

    var importName = attributes.getString("value");
    return new String[] {getMappingName(importName)};
  }

  private String getMappingName(String importName) {
    // if-else based on params
    return CustomConfig.class.getName();
  }
}
```
```java
@Configuration
public class CustomConfig {
  // ...
}
```

> **DeferredImportSelector** that runs after all @Configuration beans have been processed

### ImportAware
Enable~ 에서 import 한 @Configuration class 에서 annotation 의 value 를 가져올 때 사용한다.

```java
@Import(CustomConfig.class)
public @interface EnableCustomFunc {
  String value();
}
```
```java
@Configuration
public class CustomConfig implements ImportAware {
  private String importClassName;
  
  @Override
  public void setImportMetadata(annoatationMetadata: AnnotationMetadata) {
    Map<String, Object> attrMap = importingClassMetadata.getAnnotationAttributes(EnableCustomFunc.class.getName());
    AnnotationAttributes attributes = AnnotationAttributes.fromMap(attrMap);

    var importName = attributes.getString("value");
    this.importClassName = importName;
  }
}
```

### ImportBeanDefinitionRegistrar
Enable~ 에서 import 한 class 에서 register bean programmatically (@Configuration class 가 아니다)

```java
@Import(CustomRegistrar.class)
public @interface EnableCustomFunc {
  String value();
}
```
```java
public class CustomRegistrar implements ImportBeanDefinitionRegistrar {
  private final static String BEAN_NAME = "restTemplate";

  @Override
  public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
    Map<String, Object> attrMap = importingClassMetadata.getAnnotationAttributes(EnableCustomFunc.class.getName());
    AnnotationAttributes attributes = AnnotationAttributes.fromMap(attrMap);

    var importName = attributes.getString("value");

    var beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(RestTemplate.class)
      .addConstructorArgValue(BeanUtils.instantiate(value))
      .getBeanDefinition();
    registry.registerBeanDefinition(BEAN_NAME, beanDefinition);
  }
}
```

## 정의 순서
### @DependsOn
```java
@Configuration
public class DemoConfig {
  @Bean
  public Object beanA() {
    return new BeanA();
  }

  @Bean
  @DependsOn("beanA") // 순서정의
  public Object beanB() {
    return new BeanB();
  }
}
```

### As Arguments
```java
@Configuration
public class DemoConfig {
  @Bean
  public Object beanA() {
    return new BeanA();
  }

  @Bean
  public Object beanB(Object beanA) { // args 로 beanA 를 넣어서 A 가 먼저 생성됨을 보장
    return new BeanB();
  }
}
```

### SmartLifeCycle
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

