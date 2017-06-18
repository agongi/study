## ThreadLocal
ThreadLocal is used to `share variable` within **each thread's life-cycle**. It can avoid CME(Concurrency Modification Exception) in multi-thread environment.

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.04.17
ㅁ References:
 - https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html
 - https://stackoverflow.com/questions/2784009/why-should-java-threadlocal-variables-be-static
 - http://javacan.tistory.com/entry/ThreadLocalUsage
 - http://tutorials.jenkov.com/java-concurrency/threadlocal.html
```

### ThreadLocal
These variables differ from their normal counterparts in that each thread that accesses one (via its get or set method) has its own, independently initialized copy of the variable. ThreadLocal instances are typically private static fields in classes that wish to associate state with a thread.

#### Usage
```java
public class ThreadId {
    // Atomic integer containing the next thread ID to be assigned
    private static final AtomicInteger nextId = new AtomicInteger(0);

    // Thread local variable containing each thread's ID
    private static final ThreadLocal<Integer> threadId =
        new ThreadLocal<Integer>() {
            @Override protected Integer initialValue() {
                return nextId.getAndIncrement();
        }
    };

    // Returns the current thread's unique ID, assigning it if necessary
    public static int get() {
        return threadId.get();
    }
}
```

#### Why Static
Because if it were an instance level field, then it would actually be "Per Thread - Per Instance", not just a guaranteed "Per Thread." That isn't normally the semantic you're looking for.

Usually it's holding something like objects that are scoped to a User Conversation, Web Request, etc. You don't want them also sub-scoped to the instance of the class.

One web request => one Persistence session.

Not one web request => one persistence session per object.

### InheritableThreadLocal
This class extends ThreadLocal to provide inheritance of values from parent thread to child thread: when a child thread is created, the child receives initial values for all inheritable thread-local variables for which the parent has values.
