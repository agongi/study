## Synchronized

```
ㅁ Author: suktae.choi
- http://javarevisited.blogspot.kr/2011/04/synchronization-in-java-synchronized.html
```

특정 코드수행부분에 lock 을 건다:

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

```
private static class Counter {
  private static int count = 0;

  // method lock
  public synchronized void increment() {
    System.out.println(++count);
  }
}
```

> 묵시적으로 Intrinsic lock 을 사용한다.