## Queue

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html
```

### Queue Implementations
- PriorityQueue - an unbounded priority queue backed by a heap

### Concurrent packages
- ConcurrentLinkedQueue - an unbounded thread-safe FIFO queue based on linked nodes
  - ConcurrentLinkedDeque
- LinkedBlockingQueue - an optionally bounded FIFO blocking queue backed by linked nodes
  - LinkedBlockingDeque

- ArrayBlockingQueue - a bounded FIFO blocking queue backed by linked nodes
- LinkedTransferQueue - an unbounded TransferQueue based on linked nodes
- PriorityBlockingQueue - an unbounded blocking priority queue backed by a heap
- DelayQueue - a time-based scheduling queue backed by a heap
- ~~SynchronousQueue - a simple rendezvous mechanism that uses the BlockingQueue interface~~

> blocking is that producers may wait for consumers to receive elements

