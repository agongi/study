## Static Provider

```
ㅁ Author: suktae.choi
- https://stackoverflow.com/questions/21827548/spring-get-current-applicationcontext
```

### Static Bean / Property Provider
- Bean Provider
  - Get bean from static applicationContext field implements `ApplicationContextAware`
- Property Provider
  - Get property from static StringValueResolver field implements `EmbeddedValueResolverAware`
- PropertiesLoaderUtils
  - Get values programmatically

```java
/**
 * @author suktae.choi
 */
@Slf4j
@Component
public class RedisTypeProvider implements ApplicationContextAware, EmbeddedValueResolverAware {

    @Value("${deploy.phase}")
    private String phase;

    private static ApplicationContext CONTEXT;
    private static StringValueResolver RESOLVER;


    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        CONTEXT = applicationContext;
    }

    @Override
    public void setEmbeddedValueResolver(StringValueResolver stringValueResolver) {
        RESOLVER = stringValueResolver;
    }


    public static String getPhase() {
        // get property correspond to @Value("${deploy.phase}")
        String phase = RESOLVER.resolveStringValue("${deploy.phase}");
        log.info("phase: {}", phase);

        return phase;
    }

    public static RedisTypeProvider getRedisTypeProvider() {
        // static specific bean provider
        return (RedisTypeProvider) CONTEXT.getBean("redisTypeProvider");
    }

    public static Object getBean(String name) throws NoSuchBeanDefinitionException {
        // static bean provider (for test)
        return Optional.ofNullable(CONTEXT.getBean(name)).orElseThrow(() -> new NoSuchBeanDefinitionException(name));
    }
}
```

```java
/**
 * @author suktae.choi
 */
@Slf4j
public class ApplicationTest {

    public static void main(String args[]) {
        AbstractApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext-test.xml");

        RedisTypeProvider provider1 = (RedisTypeProvider) ctx.getBean("redisTypeProvider");
        log.info("provider1: {}", provider1);

        RedisTypeProvider provider2 = (RedisTypeProvider) RedisTypeProvider.getBean("redisTypeProvider");
        log.info("provider2: {}", provider2);

        Assert.assertEquals(provider1, provider2);

        String phase = RedisTypeProvider.getPhase();
        log.info("phase: {}", phase);

        ctx.registerShutdownHook();
    }
}
```

### [PropertiesLoaderUtils](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/core/io/support/PropertiesLoaderUtils.html)
```java
Resource resource = new ClassPathResource("/my.properties");
Properties props = PropertiesLoaderUtils.loadProperties(resource);
```
