## JDK 9

```
ㅁ Author: suktae.choi
- https://medium.com/@goinhacker/java-9%EC%9D%98-%EB%B3%80%ED%99%94%EC%99%80-%ED%8A%B9%EC%A7%95-%EB%8C%80%EC%B6%A9-%EC%A0%95%EB%A6%AC-fca77cee88f2
- http://openjdk.java.net/projects/jigsaw/quick-start
```

JDK 9 - http://openjdk.java.net/projects/jdk9/

***

## Immutable Collections

Native 로 immutable collection 생성이 가능해짐

> 기존에는 guava 를 써서 만들었음

```java
// before
ImmutableList.of(1, 2, 3);
ImmutableSet.of(1, 2, 3);
ImmutableMap.of(
	"k1", "v1",
  "k2", "v2",
);
  
// after
List.of(1, 2, 3);
Set.of(1, 2, 3);
Map.of(
  "k1", "v1",
  "k2", "v2"
);
```

## Process API

프로세스 관리 API 추가

```java
@Test
public void processTest() {
  ProcessHandle process = ProcessHandle.current();

  log.info("pid={}, info={}", process.pid(), process.info());

  // kill children
  process.children().forEach(child -> child.destroy());
}

// console log
13:47:32.300 [main] INFO  pid=68048, info=[user: Optional[user], cmd: /Users/user/.sdkman/candidates/java/11.0.6.hs-adpt/bin/java, args: [-ea, -Didea.test.cyclic.buffer.size=8388608, -javaagent:/Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar=60474:/Applications/IntelliJ IDEA.app/Contents/bin, -Dfile.encoding=UTF-8, -classpath, /Applications/IntelliJ IDEA.app/Contents/lib/idea_rt.jar:/Applications/IntelliJ IDEA.app/Contents/plugins/junit/lib/junit5-rt.jar, -ideVersion5, -junit5, com.naver.metis.core.web.rest.ApiErrorResponseTest,processTest], startTime: Optional[2020-04-01T04:47:31.090Z], totalTime: Optional[PT1.672758S]]
```

## Resource References (try-with-resources)

try-with-resource block 에 effective final or final variable 일 경우 레퍼런스가 가능해졌습니다.

> 기존에는 무조건 해당 block 안에서 정의해야 사용 가능했었음

```java
/**
* JDK 8
*/
@Test
public void resourceReferenceTest() {
  // defines in block
  try (InputStream io = new ByteArrayInputStream(new byte[0])) {
    int read = io.read();
    // do something
  } catch (Exception e) {
    log.error("some errors", e);
  }
}

/**
* JDK 9
*/
@Test
public void resourceReferenceTest() {
  InputStream io = new ByteArrayInputStream(new byte[0]);

  // resource could be referenced
  try (io) {
    int read = io.read();
    // do something
  } catch (Exception e) {
    log.error("some errors", e);
  }
}
```

## Interface Private Method

인터페이스에 private method 사용가능

```java
interface User {
  Map<Long, User> userMap = new HashMap<>();

  default User getUser(Long id) {
    return userById(id);
  }

  // private method
  private User userById(Long id) {
    return userMap.get(id);
  }
}
```

## Optional to Stream

Optional 에서 Stream 으로 바로 갈 수 있다.

> 기존에는 Optional 닫은후에, Stream 생성해서 진행했음

```java
/**
* JDK 8
*
* Optional#orElse, orElseGet 으로 닫은후에, Stream 으로 갈수있음
*/
@Test
public void optionalTest() {
  // primitive or object
  String testStr = "test";
  Stream.of(Optional.ofNullable(testStr).orElse(""))
    .map(/* testPurpose */ Function.identity())
    .forEach(System.out::println);

  // collection
  List<String> list = new ArrayList<>();
  Optional.ofNullable(list).orElse(List.of()).stream()
    .map(/* testPurpose */ Function.identity())
    .forEach(System.out::println);
}

/**
* JDK 9
*
* Optional#stream 으로 바로 갈수있음
*/
@Test
public void optionalTest() {
  // primitive or object
  String testStr = "test";
  Optional.ofNullable(testStr).stream()
    .map(/* testPurpose */ Function.identity())
    .forEach(System.out::println);

  // collection
  List<String> list = new ArrayList<>();
  Optional.ofNullable(list).stream()
    .map(/* testPurpose */ Function.identity())
    .forEach(System.out::println);
}
```

## 더 이상의 자세한 설명은 생략한다.

### [Jigsaw](https://greatkim91.tistory.com/197)

### [Flow API](https://www.hascode.com/2018/01/reactive-streams-java-9-flow-api-rxjava-and-reactor-examples/)

Flow package 에 Reactive Stream API 스펙을 구현한 네이티브 pub/sub 구현체 제공

```java
java.util.concurrent.Flow;
java.util.concurrent.Flow.Publisher;
java.util.concurrent.Flow.Subscriber;
java.util.concurrent.Flow.Processor;
```

### [HTTP/2 Client](https://www.baeldung.com/java-9-http-client)

HttpUrlConnection 대체 목적으로 추가되었고, 동기/비동기 호출가능.

 `java.incubator.http` 패키지로 들어감

> JDK 11 에서 패키지 & 사용법 변경됨 -> java.net.http

```java
@Test
public void httpClientTest() {
  HttpRequest request = HttpRequest
    .create(URI.create("http://some.api.com/user"))
    .body(noBody())
    .GET();

  // 동기 호출
  HttpResponse response = request.send();

  // 비동기 호출
  CompletableFuture<HttpResponse> responseFuture = request.sendAsync();
}
```

### [Multi-Release Jars](https://www.baeldung.com/java-multi-release-jar)

### 