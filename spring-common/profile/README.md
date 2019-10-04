## Profile

```
ㅁ Author: suktae.choi
ㅁ References:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
```

Configuration could be loaded in specific `active-profile` by programmatic:

```java
@Configuration
@Profile("dev")
public class DatabaseConfiguration {
  @Bean
  public DataSource dataSource() {
		// ...
    return null;
  }
}
```

You can preset profile in context-load phase.

```java
public static void main(String args[]) {
  ApplicationContext context = new AnnotationConfigApplicationContext();
  context.getEnvironment().setActiveProfiles("dev");
  context.refresh();
}
```

```shell
-Dspring.profiles.active=dev
```

