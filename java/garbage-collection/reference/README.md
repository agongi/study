## Garbage Collection - Detail

```
ㅁ Author: suktae.choi
ㅁ References:
- https://d2.naver.com/helloworld/329631
- http://darksilber.tistory.com/entry/Java-Reference-Object%EC%9D%98-%EC%9D%B4%ED%95%B4%EC%99%80-%ED%99%9C%EC%9A%A9strongweak-reference
- http://javarevisited.blogspot.kr/2014/03/difference-between-weakreference-vs-softreference-phantom-strong-reference-java.html
```

### References
Garbage Collector reclaims memory from objects which are eligible for garbage collection, but not many programmer knows that this eligibility is decided based upon **which kind of references are pointing to that object.**

<img src="images/Screen%20Shot%202017-08-16%20at%2022.03.11.gif" width="75%">

### Reference Type

#### Strong Reference

Strong referenced object never be collected.

```java
// strong reference
Counter counter = new Counter();
```

#### Soft Reference

- referent: It can be a candidate of GC If JVM absolutely needs memory
  - It means heap is running out, soft-reference object will be collected even if there are references
- reference-queue: optional
  - 생성자를 통해 reference-queue 가 주입되었으면, enqueued

```java
// soft reference
SoftReference<Counter> softCounter = new SoftReference<>(new Counter());
// strong reference
Count counter = softCounter.get();

// strong reference is removed, only soft remains
counter = null;

/** 
 * result
 */
new Counter() remains	// softly-referenced object (can be collected up to JVM memory)
new SoftReference() itself remains	// soft-reference itself
```

> ttl == JVM-option (-XX:SoftRefLRUPolicyMSPerMB) * (remain-heap-size) after last GC.

#### Weak Reference

- referent: It will be collected even if there are references in next GC even if there are references
- reference-queue: optional
  - 생성자를 통해 reference-queue 가 주입되었으면, enqueued

```java
// weak reference
WeakReference<Counter> weakCounter = new WeakReference<>(new Counter());
// strong reference
Count counter = weakCounter.get();

// strong reference is removed, only weak remains
counter = null;

/** 
 * result
 */
new Counter() remains	// weakly-referenced object (will be collected in next-GC)
new SoftReference() itself remains	// weak-reference itself
```

> It is useful for cache, that is to have reference but may be or not be null.

#### Phantom Reference

- referent: It will **not** be collected by GC but explicit Reference#clear is invoked
- reference-queue: mandatory

```java
// phantom reference
ReferenceQueue refQueue = new ReferenceQueue();
PhantomReference<Counter> phantomCnt = new PhantomReference<>(new Counter(), refQueue);

// phantom reference is already null
assertNull(phantomCnt.get());

/** 
 * result
 */
new Counter() remains	// phantomly-referneced (will not be collected until #clear invoked)
new PhantomReference() itself remains	// phantom-reference itself is in queue
```

#### Reference Queue

SoftReference 객체나 WeakReference 객체가 참조하는 객체가 GC 대상이 되면 SoftReference 객체, WeakReference 객체 내의 참조는 null로 설정되고 SoftReference 객체, WeakReference 객체 자체는 ReferenceQueue에 enqueue된다. 

SoftReference와 WeakReference는 ReferenceQueue를 사용할 수도 있고 사용하지 않을 수도 있다. 그러나 PhantomReference는 반드시 ReferenceQueue를 사용해야만 한다. PhantomReference의 생성자는 단 하나이며 항상 ReferenceQueue를 인자로 받는다.

GC가 객체를 처리하는 순서:

1. soft references
2. weak references
3. `파이널라이즈`
4. phantom references
5. 메모리 회수

\#finalize 이후 phantomly-reable 이 아닌 객체는 바로 메모리 회수되지만, phantom-reference 가 있다면 명시적으로 dequeue 후 clear 를 호출해서 메모리 회수를 해야한다.

즉 아래와 같이 코드상으로 clearup 을 관여할수있다:

```java
// dequeue
PhantomReference<Count> cntRef = refQueue.poll();
/* .. some clear logic */

// memory-clear
cntRef.clear();
```
