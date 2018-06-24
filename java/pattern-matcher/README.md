## Pattern - Matcher

```
ㅁ Author: suktae.choi
ㅁ Date: 2018.06.24
```

```java
/**
 * @author suktae.choi
 */
@Slf4j
public class PatternTest {
    private static final String REGEX = "^[a-zA-Z0-9]+$";
    private static final Pattern PATTERN = Pattern.compile(REGEX);

    @Test
    public void findTest() {
        Matcher matcher = PATTERN.matcher("hellohellohello1234!@#&%4--");

        StringBuffer sb = new StringBuffer();
        int matchedCount = 0;

        while (matcher.find()) {
            matchedCount++;
            matcher.appendReplacement(sb, "");
        }

        String matchedResult = matcher.appendTail(sb).toString();

        log.info("result: {}, matchedCount: {}", matchedResult, matchedCount);
    }

    @Test
    public void replaceAllTest() {
        Matcher matcher = PATTERN.matcher("hellohellohello1234!@#&%4--");

        String matchedResult = matcher.replaceAll("");

        log.info("result: {}", matchedResult);
    }
}
```
