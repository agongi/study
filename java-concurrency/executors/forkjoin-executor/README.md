## ForkJoin vs Executor
```
ㅁ Author: suktae.choi
ㅁ References:
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
