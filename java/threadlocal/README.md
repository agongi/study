## ThreadLocal

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/43851389/if-thread-local-map-contains-a-weak-reference-to-the-threadlocal-object-then-wh
- https://stackoverflow.com/questions/2784009/why-should-java-threadlocal-variables-be-static
- https://stackoverflow.com/questions/17968803/threadlocal-memory-leak
```

ThreadLocal 은 thread-scope context 를 저장할때 사용한다.

### Usage

```java
public class SessionFilter extends OncePerRequestFilter {
  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {    
    SessionContextHolder.setSession(new Session(request.getRequestedSessionId());

    try {
      filterChain.doFilter(request, response);
    } finally {
      // request-scope context clear
      SessionContextHolder.clear();
    }
  }
}
```

```java
public class SessionContextHolder {
  private static ThreadLocal<SoftReference<Session>> context = new ThreadLocal<>();

  public static Session getSession() {
    if (context.get() == null) {
      return null;
    }

    return context.get().get();
  }

  public static void setSession(Session session) {
    context.set(new SoftReference<>(session));
  }

  public static void clear() {
    context.remove();
  }
}
```

web request 가 왔을때 간단히 session value 를 저장하는 예제이다. ThreadLocal 을 통해 저장하면 per-thread 단위의 context 가 map 으로 관리되어 동시성문제도 발생하지 않는다.

그리고 필요가 없어진 context 는 memory-leak 방지를 위해 clear 를 명시적으로 해주어야 한다.

#### Why Static
Because if it were an instance level field, then it would actually be "Per Thread - Per Instance", not just a guaranteed "Per Thread." That isn't normally the semantic you're looking for.

Usually it's holding something like objects that are scoped to a User Conversation, Web Request, etc. You don't want them also sub-scoped to the instance of the class.

> One web request => one Persistence session.
>
> Not one web request => one persistence session per object.

### Details

#### Store

ThreadLocal 은 단순히 getter/setter 를 제공하는 wrapper 이다. 실제 data 는 Thread 에 저장된다.

대신 Thread 에서는 Map<ThreadLocal, Object> 의 map 을 사용해서

- key: threadLocal
- value: T (given)

으로 구분해서 각 threadlocal 에서 저장한 value 를 get/set 한다.

```java
/**
 * Thread.java
 */
ThreadLocal.ThreadLocalMap threadLocals = null;

static class ThreadLocalMap {
  static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
      super(k);
      value = v;
    }
  }
}
```

```java
/**
 * ThreadLocal.java
 */
public void set(T value) {
  Thread t = Thread.currentThread();
  ThreadLocalMap map = getMap(t);
  if (map != null)
    map.set(this, value);
  else
    createMap(t, value);
}
```

#### GC

우선 아래 케이스가 GC 대상인지 확인해보자:

- Map.Entry<> 의 key = null;

```java
public static void main(String[] args) throws InterruptedException {
  Map<String, Object> weakMap = new WeakHashMap<>();
  String key = "key";
  weakMap.put(key, "value");

  assertTrue(weakMap.containsKey(key));
  // strong-reference pointless
  key = null;

  System.gc();
  TimeUnit.SECONDS.sleep(5);
  // weakRef Entry<> removed
  assertTrue(!weakMap.containsKey(key));
}
```

> Map 자체의 reference 가 있지만
>
> -   Map<String, Object> weakMap = ...
>
> element 가 직접 참조 되지않으면 reference-type 에 따라 GC 대상이 된다.

그렇다면 Thread 내부 구현을 살펴보자.

```java
ThreadLocal.ThreadLocalMap threadLocals = null;

static class ThreadLocalMap {
  private Entry[] table;

  /**
   * key(threadLocal) 는 WeakReference() 로 관리
   */
  static class Entry extends WeakReference<ThreadLocal<?>> {
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
      super(k);
      value = v;
    }
  }
  
  /**
   * key(threadLocal)#hashCode 로 index 계산후, return table[indx];
   */
  private Entry getEntry(ThreadLocal<?> key) {
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
      return e;
    else
      return getEntryAfterMiss(key, i, e);
  }

  /**
   * key(threadLocal)#hashCode 로 index 계산후, table[indx] = new Entry(k,v);
   */
  private void set(ThreadLocal<?> key, Object value) {
    Entry[] tab = table;
    int len = tab.length;
    int i = key.threadLocalHashCode & (len-1);

    for (Entry e = tab[i];
         e != null;
         e = tab[i = nextIndex(i, len)]) {
      ThreadLocal<?> k = e.get();

      if (k == key) {
        e.value = value;
        return;
      }

      if (k == null) {
        replaceStaleEntry(key, value, i);
        return;
      }
    }

    tab[i] = new Entry(key, value);
    int sz = ++size;
    if (!cleanSomeSlots(i, sz) && sz >= threshold)
      rehash();
  }
}
```

2가지 특징은

- Entry<ThreadLocal, Object> 가 table[] array 로 관리
- key 는 weakReference()

다시말하면 key 가 strong-reference 되지 않으면, GC 에 의해 element (== Entry<TL, Obj>) 는 언제든지 clean 될수있다.

> Even though table[] has `that` Entry and still strong-referenced.

그러나 일반적인 web-application + threadLocal 을 사용하는 유형을 보면

- private static final 로 항상 참조됨 (== strong-reference)
- thread 는 pool 에서 재사용되므로, 값이 clear 되지 않는다 (== thread never die)

코드로 표현하면 다음과 같다:

```java
// referent forever
private static final ThreadLocal<Object> context = new ThreadLocal<>();

// this table in thread never die (thread is pooling)
Thread#ThreadLocal.ThreadLocalMap table[];
table[i]; // is that one.
```

Entry 는 항상 참조되어 삭제되지 않으므로 memory-leak 이 걱정된다면, value 를 weak/soft reference 로 생성하자

```java
// referent forever
private static final ThreadLocal<WeakReference<Object>> context = new ThreadLocal<>();
```

value 가 weak/soft reference 로 정의되면, strong-reference 가 없을시 GC 를 통해 수집된다.
