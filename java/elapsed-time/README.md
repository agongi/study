## Elapsed Time

```
@author: suktae.choi
- https://www.baeldung.com/java-measure-elapsed-time
- (why nanos preferred) https://namocom.tistory.com/649
```

## System.currentTimeMills

일반적으로 사용하는 방식. 하지만 SYSTEM 시간에 영향을 받는다.

> OS 의 time 에 의존하므로, 외부적인 요인으로 시간값이 바뀌면 결과가 달라짐

```java
@Test
public void currentMillsTest() throws InterruptedException {
  long start = System.currentTimeMills();
  Thread.sleep(3000); // 3s

  log.info("{}", (System.currentTimeMills() - start) / 1_000);
}
```

## System.nanoTime

JVM 단에서의 절대시간을 기준으로 측정. 그래서 엄밀히 말하면 프로그램의 소요시간을 측정할때에 적합하다.

> 대신 다른 JVM 과의 tick 은 다를 수 있으므로, 동일 JRE 에서의 측정값만 유효하다

```java
@Test
public void NanoTimeTest() throws InterruptedException {
  long start = System.nanoTime();
  Thread.sleep(3000); // 3s

  log.info("{}", (System.nanoTime() - start) / 1_000_000);
}
```

## Instant

Java 8 이후부터 가능하지만, 객체를 생성하게 된다.

```java
@Test
public void currentMillsTest() throws InterruptedException {
  Instant start = Instant.now();
  Thread.sleep(3000); // 3s

  log.info("{}", Duration.between(start, Instant.now()).toMillis() / 1_000);
}
```

## StopWatch (apache)

Apache, Spring 모두 구현체가 있지만

- Apache: nano
- Spring: mills

을 이용한다. 이것또한 불필요한 객체생성이 발생한다.

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

## 