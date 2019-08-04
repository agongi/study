## Resource

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.mkyong.com/java/java-properties-file-examples/
```

### Resource

```java
/**
* Resource 를 이용해 명시적으로 해당 Path 의 properties load
*/
private static class ContextHolder {
  private static final String DEFAULT_PROPERTIES = "application.properties";
  private static final Map<String, String> propMap;

  static {
    Resource resource = new ClassPathResource(DEFAULT_PROPERTIES);
    Properties properties = new Properties();

    try {
      properties.load(new InputStreamReader(resource.getInputStream(), StandardCharsets.UTF_8.name()));
    } catch (IOException e) {
      throw new BaseRuntimeException("properties load 실패");
    }

    propMap = Collections.unmodifiableMap(properties);
  }
```

### ResourceUtils

```java
/**
 * Spring#ResourceUtils 로 properties load
 */
public static void main(String[] args) throws IOException {
  File file = ResourceUtils.getFile("classpath:application.yml");

  Properties prop1 = new Properties();
  prop1.load(new FileInputStream(file));

  Properties prop2 = new Properties();
  prop2.load(new FileReader(file, StandardCharsets.UTF_8));

  System.out.println(prop1);
  System.out.println(prop2);
}
```

### Environment

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

### @Value

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
