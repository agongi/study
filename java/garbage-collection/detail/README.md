## Garbage Collection - Detail

```
ㅁ Author: suktae.choi
ㅁ References:
- http://darksilber.tistory.com/entry/Java-Reference-Object%EC%9D%98-%EC%9D%B4%ED%95%B4%EC%99%80-%ED%99%9C%EC%9A%A9strongweak-reference
- http://javarevisited.blogspot.kr/2014/03/difference-between-weakreference-vs-softreference-phantom-strong-reference-java.html
```

### References
Garbage Collector reclaims memory from objects which are eligible for garbage collection, but not many programmer knows that this eligibility is decided based upon **which kind of references are pointing to that object.**

<img src="images/Screen%20Shot%202017-08-16%20at%2022.03.11.gif" width="75%">

#### Reference Type
- Strong Reference: Strong referenced instance never be a candidates of GC
```java
// strong reference
Counter counter = new Counter();
```

- Soft Reference: It can be a candidate of GC If JVM absolutely needs memory.

 It has more change to be survived once it is referenced many times before
```java
// strong reference
Counter counter = new Counter();
// soft reference
SoftReference<Counter> weakCounter = new SoftReference<>(counter);
// strong reference is removed, only soft remains
counter = null;
```

- Weak Reference: Weak referenced instance has to be cleaned once GC runs. It lives until next GC
```java
// strong reference
Counter counter = new Counter();
// weak reference
WeakReference<Counter> weakCounter = new WeakReference<>(counter);
// strong reference is removed, only weak remains
counter = null;
```

- Phantom Reference: It is not accessible using .get() but still remains unreachable state. This type is used to handle after finalize() for clean-up
```java
// strong reference
Counter counter = new Counter();
// weak reference
PhantomReference<Counter> weakCounter = new PhantomReference<>(counter);
// strong reference is removed, only weak remains
counter = null;
```

#### Reference Queue
Reference of instance will be appended to ReferenceQueue and you can use it to perform any clean-up by polling ReferenceQueue.
```java
// reference will be stored in this queue for cleanup
ReferenceQueue refQueue = new ReferenceQueue();
DigitalCounter digit = new DigitalCounter();
// reference queue is added in xxxReference instantiation
PhantomReference<DigitalCounter> phantom = new PhantomReference<DigitalCounter>(digit, refQueue);
```
