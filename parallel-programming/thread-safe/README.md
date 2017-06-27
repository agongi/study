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
  - Whole method
  - Specific operation
- Use Concurrent packages

```java
public class Counter {
    private int count = 0;
    private AtomicInteger atomicCount = new AtomicInteger(0);
    private List<Integer> list = new ArrayList<>();


    public synchronized int getCount(){
        return count++;
    }

    public void updateList(Integer element) {
        synchronized (list) {
            list.add(element);
        }
    }

    public int getCountAtomically(){
        return atomicCount.incrementAndGet();
    }
}
```
