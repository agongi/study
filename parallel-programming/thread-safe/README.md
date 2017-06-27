## Thread-Safe Code in a Nutshell

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.27
ㅁ References:
 - http://javarevisited.blogspot.kr/2012/01/how-to-write-thread-safe-code-in-java.html
```

### Non Thread-Safe
```java
/*
 * Non Thread-Safe Class in Java
 */
public class Counter {
    private int count;

    /*
     * This method is not thread-safe because ++ is not an atomic operation
     */
    public int getCount(){
        return count++;
    }
}
```

### Thread-Safe
- Use synchronized
- Use Concurrent packages

```java
public class Counter {
    private int count = 0;
    private AtomicInteger atomicCount = new AtomicInteger(0);

    /*
     * This method thread-safe now because of locking and synchronization
     */
    public synchronized int getAndIncrease(){
        return count++;
    }

    /*
     * This method is thread-safe because count is incremented atomically
     */
    public int increaseAndGet(){
        return atomicCount.incrementAndGet();
    }
}
```
