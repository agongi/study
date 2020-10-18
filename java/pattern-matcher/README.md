## Pattern/Matcher

```
ㅁ Author: suktae.choi
```

```java
/**
 * @author suktae.choi
 */
@Slf4j
public class PatternTest {
  private static final Pattern PATTERN = Pattern.compile("^[a-zA-Z0-9]+$");

  @Test
  public void replaceAllTest() {
    Matcher matcher = PATTERN.matcher("hellohellohello1234!@#&%4--");

    String matchedResult = matcher.replaceAll("");
    log.info("result={}", matchedResult);
  }
}
```

