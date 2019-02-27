## Thread Pool
```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/9276807/whats-the-advantage-of-a-java-5-threadpoolexecutor-over-a-java-7-forkjoinpool
- http://javarevisited.blogspot.kr/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html
```

### ForkJoinPool vs Executor Framework
#### ForkJoinPool (== JVM default since JDK.7, used in parallelStream())
- Worker - Runnable or Callable
- Task - one per thread
- Queue - One global queue for all shared tasks
  - It may cause performance **bottleneck once frequent enqueue/dequeue** occurs due to concurrent operation

#### Executor Framework
- Worker - ForkJoinTask
  - It is **much smaller (lighter)** than callable or runnable
- Task - Task **divides** separately and each **subtasks** are assigned to threads
- Queue - One global queue for accepting submit, all thread **hold each queue** separately
  - Task will be distributed to each threads and enqueue in thread queue
  - If one is **free to steal**, It dequeues one from it

### Types
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
