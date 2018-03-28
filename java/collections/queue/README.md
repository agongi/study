## Queue

```
ㅁ Author: suktae.choi
ㅁ References:
- http://oniondev.egloos.com/558949
```

### Queue Implementations
#### ArrayBlockingQueue
A bounded blocking queue backed by an array. This queue orders elements FIFO (first-in-first-out)

#### LinkedBlockingQueue
An unbounded (optionally-bounded) blocking queue based on linked nodes. This queue orders elements FIFO (first-in-first-out)

By default, capacity is defined Integer.MAX_VALUE

#### TransferQueue
LinkedTransferQueue

A blocking queue in which producers may wait for consumers to receive elements.<br>
a queue with 0 capacity, such as SynchronousQueue, put and transfer are effectively synonymous.

#### SynchronousQueue
A blocking queue in which each insert operation must wait for a corresponding remove operation by another thread, and vice versa (capacity 0)

#### PriorityQueue
An unbounded priority queue based in which elements are ordered in natural-order, or by a Comparator provided at construct time.<br>
A priority does not permit insertion of non-comparable objects (doing so may result in ClassCastException).

> PriorityBlockingQueue is blocking type of PriorityQueue

#### DelayQueue
An unbounded blocking queue of Delayed elements, in which an element can only be taken when its delay has expired

### Concurrent packages
#### ConcurrentLinkedQueue
An unbounded thread-safe queue based on linked nodes. This queue orders elements FIFO (first-in-first-out)
