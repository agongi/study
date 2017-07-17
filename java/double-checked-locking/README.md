## Double Checked Locking

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.07.17
ㅁ References:
 - http://javarevisited.blogspot.kr/2014/05/double-checked-locking-on-singleton-in-java.html
```

```java
/**
 * @author suktae.choi
 */
@Slf4j
public class DoubleCheckedTest {
    private static DoubleCheckedTest instance = null;

    public DoubleCheckedTest getInstance() {
        if (instance == null) {     // check condition in concurrent
            synchronized (DoubleCheckedTest.class) {    // enter synchronized block simultaneously
                if (instance == null) {     // check condition again, and lock until one thread completed
                    instance = new DoubleCheckedTest();
                }
            }
        }

        return instance;
    }

    public static void main(String[] args) {
        // ...
    }
}
```

- Thread can **join synchronized block simultaneously**
- Check again in block to guarantee make it instance once only
