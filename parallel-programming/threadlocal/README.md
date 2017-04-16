## ThreadLocal
ThreadLocal is used to `share variable` within **each thread's life-cycle**. It can avoid CME(Concurrency Modification Exception) in multi-thread environment.

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.04.17
ㅁ References:
 - http://javacan.tistory.com/entry/ThreadLocalUsage
 - http://tutorials.jenkov.com/java-concurrency/threadlocal.html
```

### Code block variable
```java
{
  // It is only valid within this code block
  int codeBlockVariable = 10;  
}
```

### Threadlocal
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class ThreadLocalTest {

    private class ThreadLocalHolder implements Runnable {

        private ThreadLocal<Integer> threadLocal = new ThreadLocal<>();

        @Override
        public void run() {
            threadLocal.set((int) (Math.random() * 100D));

            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {

            }

            System.out.println(threadLocal.get());
        }
    }

    @Test
    public void test00_threadLocal() throws InterruptedException {
        ThreadLocalHolder runnable = new ThreadLocalHolder();

        Thread thread1 = new Thread(runnable);
        Thread thread2 = new Thread(runnable);

        thread1.start();
        thread2.start();

        thread1.join(); //wait for thread 1 to terminate
        thread2.join(); //wait for thread 2 to terminate
    }
}
```

Each threads cannot see each other's values. Thus, they set and get different values.

### InheritableThreadLocal
```java
private class ThreadLocalHolder implements Runnable {

    private ThreadLocal<Integer> threadLocal = new InheritableThreadLocal<>();

    @Override
    public void run() {
        threadLocal.set((int) (Math.random() * 100D));

        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {

        }

        System.out.println(threadLocal.get());
    }
}
```

Instead of each thread having its own value inside a ThreadLocal, the InheritableThreadLocal is bound to all child threads created by that thread.
