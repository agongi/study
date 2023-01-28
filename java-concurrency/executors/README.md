## Thread Pool

```
ㅁ Author: suktae.choi
- https://stackoverflow.com/questions/9276807/whats-the-advantage-of-a-java-5-threadpoolexecutor-over-a-java-7-forkjoinpool
```

#### Index

- [ForkJoin vs Executor](forkjoin-executor)

### ExecutorService

#### Executors.newFixedThreadPool()
- Thread count - fixed
- Thread lifetime - 0 (infinite)
- Queue size - unlimited

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
  return new ThreadPoolExecutor(nThreads, nThreads,
    0L, TimeUnit.MILLISECONDS,
    new LinkedBlockingQueue<Runnable>());
}
```

#### Executors.newSingleThreadPool()
- Thread count - 1
- Thread lifetime - 0 (infinite)
- Queue size - unlimited

```java
public static ExecutorService newFixedThreadPool() {
  return new ThreadPoolExecutor(1, 1,
    0L, TimeUnit.MILLISECONDS,
    new LinkedBlockingQueue<Runnable>());
}
```

#### Executors.newCachedThreadPool()
- Thread count - unlimited
- Thread lifetime - 60s
- Queue size - 0 (hand-off)

```java
public static ExecutorService newCachedThreadPool() {
  return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
    60L, TimeUnit.SECONDS,
    new SynchronousQueue<Runnable>());
}
```

#### Executors.newWorkStealingPool()
- Thread count - upon processors count
- Queue size -  64M (static final int MAXIMUM_QUEUE_CAPACITY = 1 << 26)

```java
public static ExecutorService newWorkStealingPool() {
  return new ForkJoinPool
    (Runtime.getRuntime().availableProcessors(),
    ForkJoinPool.defaultForkJoinWorkerThreadFactory,
    null, true);
}
```

### ThreadPoolExecutor

<img src="images/Screen Shot 2019-06-27 at 01.48.23.png" width=50%>

제공되는 ExecutorService 를 상속하고, pool 생성시 customize 가능하다. 추가로 제공되는 기능도 있다:

- Hooks
- Initialize/Finalize

```java
public class PausableThreadPoolExecutor extends ThreadPoolExecutor {
    private final ExecutorService executorService = new ThreadPoolExecutor(
        15, // core thread pool size
        15, // maximum thread pool size
        1, // time to wait before resizing pool
        TimeUnit.MINUTES, // time unit
        new ArrayBlockingQueue<>(30, true), // queue
        new CustomizableThreadFactory("TEST"), // thread name prefix set
        new ThreadPoolExecutor.CallerRunsPolicy() // reject policy
    );
}
```

### ScheduledThreadPoolExecutor

```java
public class SchedulerConfiguration {
  // threadCount: 1
	private ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
  
  @PostConstruct
  public void init() {
    // .. something
    
    executor.schedule(this::init, 5, TimeUnit.MINUTES);
  }
}
```

