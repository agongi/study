## CountDownLatch

```
ㅁ Author: suktae.choi
ㅁ References:
```

### Overview

Main thread 는 task 를 sub-thread 에 위임하고, 결과를 기다린 후 처리하는 방식.

CountDown 이 0 이 되면, block 되었던 main thread 가 resume 되어 작업을 이어서 수행한다.

> reactive-stream 의 사상과 동일하다.

<img src="images/Screen%20Shot%202019-11-09%20at%2001.20.06.png">

```java
public static void main(String[] args) throws InterruptedException {
  CountDownLatch countDown = new CountDownLatch(5);
  long startMills = System.currentTimeMillis();

  System.out.println("start!");

  Runnable runnable = () -> {
    try {
      System.out.println(Thread.currentThread().getName());
      TimeUnit.SECONDS.sleep(5);
    } catch (InterruptedException e) {
      e.printStackTrace();
    } finally {
      countDown.countDown();
    }
  };

  for (int i = 0; i < 5; i++) {
    new Thread(runnable).start();
  }

  // wait until count reach 0
  countDown.await();

  System.out.println(System.currentTimeMillis() - startMills);
}

// console result
start!
Thread-1
Thread-2
...
Thread-98
Thread-99
Thread-100
5064
```

생성되는 thread 갯수의 제한이 없는것을 보면, Executors.newCachedThreadPool() 와 동일한 동작원리로 보임