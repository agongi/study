## Environment vs Resource/Properties vs @Value

```
ㅁ Author: suktae.choi
ㅁ References:
```

**Resource/Properties**
```java
/**
* Resource 를 이용해 명시적으로 해당 Path 의 properties 를 로그한다
*/
private static class ContextHolder {
    private static final String DEFAULT_PROPERTIES = "default.properties";
    private static final Map<String, String> propMap;

    static {
        Resource resource = new ClassPathResource(DEFAULT_PROPERTIES);
        Properties properties = new Properties();

        try {
            properties.load(resource.getInputStream());
        } catch (IOException e) {
            throw new BaseRuntimeException("properties load 실패");
        }

        propMap = properties.entrySet()
            .stream()
            .collect(Collectors.collectingAndThen(
                Collectors.toMap(o -> o.getKey(), o -> o.getValue()),
                Collections::unmodifiableMap
            ));
    }
```

**Environment**
```java
/**
* Environment 를 이용해 properties 를 로드한다
*/
public class LocaleConfig implements EnvironmentAware {
  // DI over EnvironmentAware
  private Environment env;

  @Bean
  public LocaleManager localeManager() {
    Properties props = new Properties();
    props.put("name", env.getProperty("default.locale"));
  }
}
```

**@Value**
```java
/**
* @Value 를 이용해 해당 키의 Property 를 가져온다
*/
public class UserService {
  @Value("${locale.title.kr}")
  private String titleNameKr;

  // ...
}
```
