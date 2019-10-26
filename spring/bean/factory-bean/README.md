## Factory Bean
Create the `uncreatable.`

```
ㅁ Author: suktae.choi
ㅁ References:
- http://whiteship.tistory.com/564
- http://stackoverflow.com/questions/4970297/how-to-get-beans-created-by-factorybean-spring-managed
```

### Scenario
- Java class which you want to make it as spring bean needs to have constructor
- Factory produces `target instance` with some configuration settings without expose to user
- Directly define `target instance` as bean will miss those configuration settings
- Any way to define factory generated instance as a bean?
- Factory class can't fulfill this condition
  - It is responsible for creating other java instance in easy
  - It only has static methods to provide
  - It doesn't have constructor itself

### Code
#### JacksonBean
```java
/**
 * @author suktae.choi
 */
public class JacksonBean {
}
```

#### JacksonFactory
```java
/**
 * @author suktae.choi
 */
public class JacksonFactory {
    private static JacksonBean jacksonBean;

    static {
        // initialize
    }

    public static JacksonBean getInstance() {
        if (jacksonBean == null) {
            synchronized (JacksonBean.class) {
                jacksonBean = new JacksonBean();
            }
        }

        return jacksonBean;
    }
}
```

> You can instantiate it using JVM guaranteed enum-singleton pattern /wo double-locking check.

#### JacksonFactoryBean

```java
/**
  * @author suktae.choi
  */
public class JacksonFactoryBean implements FactoryBean<JacksonBean> {
  private ApplicationContext applicationContext;
  private JacksonBean jacksonBean;

  @Override
  public JacksonBean getObject() throws Exception {
    if (jacksonBean == null) {
      synchronized (JacksonBean.class) {
        this.jacksonBean = JacksonFactory.getInstance();
        // apply instance in applicationContext
        applicationContext.getAutowireCapableBeanFactory().autowireBean(jacksonBean);
      }
    }

    return this.jacksonBean;
  }

  @Override
  public Class<?> getObjectType() {
    return JacksonBean.class;
  }

  @Override
  public boolean isSingleton() {
    return true;
  }
}
```

#### Main
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class ApplicationTest {
  public static void main(String args[]) {
    AbstractApplicationContext context = new ClassPathXmlApplicationContext("applicationContext-test.xml");
    // get target bean which factoryBean returned
    JacksonBean jackson = (JacksonBean) context.getBean("jackson");
    // get bean factoryBean itself
    JacksonFactoryBean jacksonFactoryBean = (JacksonFactoryBean) context.getBean("&jackson");

    log.debug("jackson={}", jackson);
    log.debug("jacksonFactoryBean={}", jacksonFactoryBean);

    container.registerShutdownHook();
  }
}
```
