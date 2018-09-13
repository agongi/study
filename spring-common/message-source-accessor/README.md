## MessageSourceAccessor

```
ㅁ Author: suktae.choi
ㅁ References:
- http://devks.tistory.com/42
```

Message can be managed with args using MessageSource.

```java
@Component
public class ExceptionContextUtils {

  private static MessageSourceAccessor messageSourceAccessor;

  @Autowired
  public void setMessageSourceAccesor(MessageSourceAccessor messageSourceAccessor) {
    this.messageSourceAccessor = messageSourceAccessor;
  }


  public static String getExceptionMessage(ExceptionContext exceptionContext) {
      return Optional.ofNullable(exceptionContext)
        .map(o -> messageSourceAccessor.getMessage(
          "city: {0}, nation: {1}",
          new String[] {"suwon", "korea"})
        .orElse(null);
    }
}
```
