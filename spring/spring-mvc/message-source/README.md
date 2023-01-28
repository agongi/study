## MessageSource

```
ㅁ Author: suktae.choi
- http://devks.tistory.com/42
```

Resource bundle could be translated into defined-locale. resource name should follow these rules:

- {fileName}\_{locale}\_{country}.properties
  - messages_ko_KR.properties
  - messages_ko.properties
  - messages.properties

Once  you are looking forward to messages in `locale.KR` (your system locale is kr), It is searching corresponding language in locale and then language matched else default.

> message_ko_KR -> (not found) -> message_ko -> (not found) -> messasge

Spring provides nice-wrapper for handling this resource-bundling:

```java
@Configuration
public class ExceptionContextUtils {

  @Bean
  public MessageSource messageSource() {
    ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
    // messages.properties
    messageSource.setBasename("classpath:/messages");
    messageSource.setDefaultEncoding("UTF-8");
    messageSource.setCacheSeconds(10);
    return messageSource;
  } 
}
```

```java
// messages_ko_KR.properties
user.welcome=안녕 {0}!!
// messages_en_US.properties
user.welcome=hello {0}!!
```

```java
@Component
public class AppRunner implements ApplicationRunner {
  @Autowired
	private MessageSource messageSource;

  @Override
  public void run(ApplicationArguments args) throws Exception {
    messageSource.getMessage("user.welcome", new String[]{"world"}, Locale.US);
  }
}
```

