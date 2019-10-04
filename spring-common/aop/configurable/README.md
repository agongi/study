## @Configurable

```
ㅁ Author: suktae.choi
ㅁ References:
- http://egloos.zum.com/springmvc/v/548980
- https://whiteship.tistory.com/1279?category=112151
```

The instance managed by spring-container can only use AOP or DI, all spring providing features. But this concept is opposite to DDD (Domain-Driven-Development).

What if domain instance like JPA @Entity wish to use spring-bean's feature over `@Autowire` ID? This is not possible in general situation but `@Configurable` will make it work.

```java
@Configuration
@EnableSpringConfigured
public class ConfigurableConfiguration {
  // ...
}
```

```java
/**
 * POJO Entity wants to use spring-service (DI)
 */
@Entity
@Table("USER")
@Configurable
public class User {
  private final FormattingService formattingService;
  
  @Column("NAME")
  private String name;
  @Column("NUMBEr")
  private String number;
  
  public User(
  	FormattingService formattingService) {
    
    this.formattingService = formattingService;
  }
  
  public String toString() {
    return name + ", " + formattingService.ofNumber(number);
  }
}
```

```bash
java -javaagent:lib/aspectweaver-x.x.jar -jar user-test.1.0.0-SNAPSHOT.jar
```

