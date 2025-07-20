# Double-Checked Locking
```
https://javarevisited.blogspot.kr/2014/05/double-checked-locking-on-singleton-in-java.html
```

- Thread can **join synchronized block at the same time**
- Check again in block to guarantee make it only once

```java
@Slf4j
public class DoubleCheckedTest {
    private static DoubleCheckedTest instance = null;

    public DoubleCheckedTest getInstance() {
        // double-checked locking
        if (instance == null) {
            // enter synchronized block N-Threads at the same time
            synchronized (DoubleCheckedTest.class) {
                // double-checked locking
                if (instance == null) {
                    instance = new DoubleCheckedTest();
                }
            }
        }

        return instance;
    }
}
```
