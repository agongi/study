## StopWatch

```
ㅁ Author: suktae.choi
ㅁ References:

```

### StopWatch
```java
@Test
public void StopWatchTest() throws InterruptedException {
    StopWatch stopWatch = new StopWatch("stopWatchTask");
    stopWatch.start();

    Thread.sleep(3000); // 3s
    stopWatch.stop();

    log.info("{}", stopWatch.prettyPrint());
}
```

### System.nanoTime
```java
@Test
public void NanoTimeTest() throws InterruptedException {
    long start = System.nanoTime();
    Thread.sleep(3000); // 3s

    log.info("{}", (System.nanoTime() - start) / 1000000);
}
```
