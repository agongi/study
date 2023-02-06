## Cyclic Barrier

```
@author: suktae.choi
```

### Overview

지정한 threshold 에 도달할때까지 Thread 들은 await 하고, 도달하면 다음 로직을 같이 수행함.

>  재사용가능하므로 cyclic 이 prefix 로 정의됨

<img src="images/cyclic-barrier.png">

```java
public static void main(String[] args) throws InterruptedException, BrokenBarrierException {
  CyclicBarrier barrier = new CyclicBarrier(5);
  System.out.println("start!");

  Runnable runnable = () -> {
    try {
      System.out.println(Thread.currentThread().getName() + " is waiting");
      barrier.await();
      System.out.println(Thread.currentThread().getName() + " is doing something");
    } catch (InterruptedException | BrokenBarrierException e) {
      e.printStackTrace();
    }
  };
  
  for (int i = 0; i < 10; i++) {
    new Thread(runnable).start();
  }
}

// console result
start!
Thread-1 is waiting
Thread-2 is waiting
Thread-3 is waiting
Thread-4 is waiting
Thread-5 is waiting
Thread-5 is doing something
Thread-6 is waiting
Thread-7 is waiting
Thread-1 is doing something
Thread-2 is doing something
Thread-3 is doing something
Thread-4 is doing something
Thread-8 is waiting
Thread-9 is waiting
Thread-10 is waiting
Thread-10 is doing something
Thread-6 is doing something
Thread-8 is doing something
Thread-9 is doing something
Thread-7 is doing something
```







