## Safepoint

```
ㅁ Author: suktae.choi
ㅁ References:
- https://medium.com/software-under-the-hood/under-the-hood-java-peak-safepoints-dd45af07d766
```

### Terms

A **safepoint** is a range of execution context that all running thread information is well described. In the point of global view, all threads must block at a safepoint before GC runs.

### Flow

- A event fired to trigger safepoint flag set true
- Thread must check safepoint flag in each executions
  - true -> block with saving all context
- `JVM keeps waiting` before all threads reach safepoint and stop
  - true -> GC runs
- Safepoint flag set false
- Thread resume previous task at the stop point from retriving information at safepoint