# Lock
```
https://happinessoncode.com/2017/10/04/java-intrinsic-lock/
```

### Blog
- [비관적 Lock, 낙관적 Lock 이해하기]([https://medium.com/@jinhanchoi1/%EB%B9%84%EA%B4%80%EC%A0%81-lock-%EB%82%99%EA%B4%80%EC%A0%81-lock-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1986a399a54](https://medium.com/@jinhanchoi1/비관적-lock-낙관적-lock-이해하기-1986a399a54))

***
## Synchronized
특정 코드 영역에 lock 을 잡습니다

```java
private static class Counter {
  private static int count = 0;
  private ReentrantLock lock = new ReentrantLock();

  public void increment() {
    // lock object
    synchronized (lock) {
      System.out.println(++count);
    }
  }
}
private static class Counter {
  private static int count = 0;

  public void increment() {
    // lock itself
    synchronized (this) {
      System.out.println(++count);
    }
  }
}
```

> this 가 동작하는 이유 -> 모든 자바 객체는 고유락 (Intrinsic lock) 을 가지고있다 == 모니터 (Monitor) 라고도 부름

아니면 method-level 에서의 전체 lock:

```java
private static class Counter {
  private static int count = 0;

  // method lock
  public synchronized void increment() {
    System.out.println(++count);
  }
}
```

> 묵시적으로 Intrinsic lock 을 사용한다.

## Mutex
### ReentrantLock
명시적으로 lock 획득/해제 를 제어할 수 있다:

```java
private static class Counter {
  private static int count = 0;
  private ReentrantLock lock = new ReentrantLock();

  public void increment() {
    lock.lock();	// block until condition holds
    try {
      // ... method body
    } finally {
      lock.unlock();
    }
  }
}
```

Read/Write 의 lock 을 구분해서 관리 할 수도 있다:

```java
private static class Counter {
  private int count = 0;
  private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

  // write lock
  public void write() {
    lock.writeLock().lock();
    try {
        count++;
    } finally {
        lock.writeLock().unlock();
    }
  }
  
  // read lock
  public int read() {
    lock.readLock().lock();
    try {
      return count;
    } finally {
      lock.readLock().unlock();
    }
  }
}
```

### double-checked locking
```java
/**
 * 싱글톤
 **/
public Object getInstance() {
  if (INSTANCE == null) {
    // block 은 1개의 Thread 만 수행하지만, 해당 lock 을 대기하는 N 개의 쓰레드 존재가능
    synchronized (this) {
      // double-check
      if (INSTANCE == null) {
        INSTANCE = new User();
      }
    }
  }

  return INSTANCE;
}
```

### Semaphore
N 개의 Thread 가 read/write 접근가능

```java
Semaphore semaphore = new Semaphore(/*permitted threads concurrently*/ 3);
```
