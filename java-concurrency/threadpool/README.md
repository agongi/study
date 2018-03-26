## Thread Pool

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/9276807/whats-the-advantage-of-a-java-5-threadpoolexecutor-over-a-java-7-forkjoinpool
- http://javarevisited.blogspot.kr/2016/12/difference-between-executor-framework-and-ForkJoinPool-in-Java.html
```


runnable callable vs ForkJoinTask
work-stealing mechanism /w fork-join vs turnkey
thread specific dequeue /w global queue vs one queue


Executors.newFixedThreadPoolExecutor()
Executors.newCachedThreadPoolExecutor()
Executors.newSingleThreadPoolExecutor()
Executors.newWorkStealingPool()
