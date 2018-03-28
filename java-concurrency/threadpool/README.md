## Thread Pool

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/9276807/whats-the-advantage-of-a-java-5-threadpoolexecutor-over-a-java-7-forkjoinpool
- http://javarevisited.blogspot.kr/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html
```

### ForkJoinPool vs Executor Framework
#### ForkJoinPool
- Runnable and/or Callable task
- Thread handles one task completely (== turnkey)
- One global queue for all shared tasks
  - It may cause performance **bottleneck once frequent enqueue/dequeue** occurs due to concurrent operation

#### Executor Framework
- ForkJoinTask
  - It is **much smaller (lighter)** than traditional callable and/or runnable
- Task **divides** separately and each **subtasks** are assigned to threads
- One global queue for accepting submit, all thread **hold each queue** separately
  - Task will be distributed to each threads and enqueue in thread queue
  - If one thread is **free to steal** task of other thread, It dequeue one from it (== from hard-working thread that holds lots of tasks)

### Types of pool
#### Executors.newFixedThreadPool()
It remains a fixed number of min/max thread alive

#### Executors.newSingleThreadPool()
It equals to Executors.newFixedThreadPool(1)

#### Executors.newCachedThreadPool()
It may adjust a number of threads until max point. It will release that once spare threads keep calm **over than default idle-time**

#### Executors.newWorkStealingPool()
It is a **wrapper of ForkJoinPool()** instead of executor framework
