## Safepoint

```
ㅁ Author: suktae.choi
ㅁ References:
- http://blog.ragozin.info/2012/10/safepoints-in-hotspot-jvm.html
- https://medium.com/software-under-the-hood/under-the-hood-java-peak-safepoints-dd45af07d766
```

## Terms

A **safepoint** is a range of execution context that all running thread information is well described. In the point of global view, all threads must block at a safepoint before GC runs.

## Flow

- A event fired to trigger safepoint flag set true
- Thread must check safepoint flag in each executions `at reasonable interval` of
  - entry/exit of method
  - any 2 bytecodes execution
  - infinite-loop at edge (countable is not considered)
- `JVM keeps waiting` before all threads reach safepoint and stop
  - true -> GC runs
- Safepoint flag set false
- Each of thread polls safepoint flags `at reasonable interval` then notified it is false
- Thread resume previous task at the stop point from retriving information at safepoint

