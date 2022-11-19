## JDK 11

```
ㅁ Author: suktae.choi
ㅁ References:
- https://futurecreator.github.io/2018/09/29/java-11-released/
- https://meetup.toast.com/posts/171
- https://mkyong.com/java/java-11-nest-based-access-control/
- https://www.baeldung.com/java-nest-based-access-control
- https://www.baeldung.com/java-reflection-change-annotation-params
- https://www.baeldung.com/jvm-epsilon-gc-garbage-collector
```

JDK 11 - http://openjdk.java.net/projects/jdk/11/

***

## [Local-Variable Syntax for Lambda Parameters](https://openjdk.java.net/jeps/323)

lambda 표현식에서도 var 사용이 가능해짐. 

원래 lambda 에서는 타입추론이 되었는데, var 를 통해 명시적으로 선언도 가능해진것

```java
@Test
public void lambdaInferenceType() {
  var list = new ArrayList<String>();

  list.stream()
    .map(s -> s.toLowerCase())
    .collect(Collectors.toList());

  // stream 에서 var 사용가능
  list.stream()
    .map((var s) -> s.toLowerCase())
    .collect(Collectors.toList());
}
```

Stream 에서 타입지정을 잘안하지만, 스펙이 들어간 목적은:

> Align the syntax of a formal parameter declaration in an implicitly typed lambda expression with the syntax of a local variable declaration.

정리하면 람다에서도 표준스펙에 맞춰서 var 사용 가능하도록 지원한것.

## [HTTP Client (Standard)](https://openjdk.java.net/jeps/321)

JDK 9 에서 추가된 HTTP 패키지가 정식으로 올라가고, 약간의 사용법이 변경되었습니다.

- java.incubator.http -> `java.net.http`

```java
@Test
public void httpclientTest() {
  // client
  HttpClient client = HttpClient.newHttpClient();

  // request
  HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("http://api.com"))
    .timeout(Duration.ofMillis(3000))
    .header("Content-Type", "application/json")
    .POST(HttpRequest.BodyPublishers.ofFile(Paths.get("classpath:json/file.json")))
    .build();

  try {
    // send
    HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());

    // send async
    CompletableFuture<HttpResponse<String>> responseFuture = client.sendAsync(request, HttpResponse.BodyHandlers.ofString());

  } catch (Exception e) {
    log.error("error!", e);
  }
}
```

신박한것도 있는데 기존응답을 가져오는 method 도 있음

```java
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
// get previous optional
Optional<HttpResponse<String>> stringHttpResponse = response.previousResponse();
```

코드를 보면 한번 생성된 `HttpRequest 는 immutable` 이고 (즉 재사용), exchange 로 응답을 만들때 기존 응답 context 를 넣어서 만들어줌.

```java
// this.response 가 기존 context, r 은 현재 진행중인 request
this.response = new HttpResponseImpl<>(r.request(), r, this.response, body, exch);
```

## [Launch Single-File Source-Code Programs](http://openjdk.java.net/jeps/330)

1개의 파일에서 main 이 있다면 빠르게 실행가능한 모드를 제공. 즉 기존의

- compile (java -> class)
- run bytecode

과정을 1개로 줄여서 직접 java 파일을 지정할수 있다.

```bash
$ cat << EOF > HelloWorld.java
public class HelloWorld {
  public static void main(String[] args) {
    System.out.print("Hello, World! ");
    for (int i=0; i<args.length; i++) {
      System.out.print(args[i] + " ");
    }
  }
}
EOF
$ javac HelloWorld.java
$ java -cp ':*' HelloWorld 1 2 3
Hello, World! 1 2 3

# 이제 이게 가능함
$ java HelloWorld.java 1 2 3 
Hello, World! 1 2 3
```

### [Nest-Based Access Control](https://openjdk.java.net/jeps/181)

Nested-Class 관계에서 아래는 동작합니다.

```java
public class Outer {
  public void outerPublic() {
    System.out.println("Outer#outerPublic");
  }

  private void outerPrivate() {
    System.out.println("Outer#outerPrivate");
  }

  public class Inner {
    public void innerPublic() {
      System.out.println("Inner#innerPublic");

      // 호출가능
      outerPrivate();
    }
  }
}
```

호출이 가능했던 이유는, Outer#private 호출을 위한 package-private bridg method 를 컴파일 단계에서 생성하기 때문입니다.

즉 아래의 형태로 컴파일러가 optimization 을 수행합니다.

```java
public class Outer {
  public void outerPublic() {
    System.out.println("Outer#outerPublic");
  }

  private void outerPrivate() {
    System.out.println("Outer#outerPrivate");
  }
  
  // bridge for private
  void outerPackagePrivate() {
    // delegate
    outerPrivate();
  }

  public class Inner {
    public void innerPublic() {
      System.out.println("Inner#innerPublic");

      // outerPrivate -> outerPackagePrivate
      outerPackagePrivate();
    }
  }
}
```

브릿지메서드로 우회한 것으로 원칙적으론 private field, method 에 대한 접근은 **본인만 가능합니다.**

[JVMS 5.4.4 - JDK 8](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-5.html#jvms-5.4.4)

```java
A field or method R is accessible to a class or interface D if and only if any of the following is true:

R is private and is declared in D. // R 을 선언한 D 만 접근이 가능
```

Nest-Based Access Control 은 스펙적으로 `nested 관계는 private 접근이 가능`하다고 정의한 것입니다.

[JVMS 5.4.4 - JDK 11](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-5.html#jvms-5.4.4)

```java
A field or method R is accessible to a class or interface D if and only if any of the following is true:

R is private and is declared by a class or interface C that belongs to the same nest as D, according to the nestmate test below.

---
If R is not accessible to D, then:

If R is public, protected, or has default access, then access control throws an IllegalAccessError.

If R is private, then the nestmate test failed, and access control fails for the same reason.

Otherwise, access control succeeds.
```

> 유의할점은 nested class 일때, private field/method 만 접근 허용이고 나머지는 IllegalAccessError 로 정의함

스펙에서 정의한 nestmate 관계는 아래와 같습니다:

```java
@Test
public void nestmateTest() {
  // Outer class 의 host 는 Outer Class (본인) 이다.
  assertEquals(Outer.class.getNestHost().getSimpleName(), "Outer");

  String outerNestMembers = Arrays.stream(Outer.class.getNestMembers())
    .map(Class::getSimpleName)
    .collect(Collectors.joining(","));

  // Outer class 의 members 는 본인포함 + Nest-Class 이다.
  assertEquals(Outer.class.getNestMembers().length, 2);
  assertEquals(outerNestMembers, "Outer,Inner");

  // Inner class 의 host 는 Outer Class 이다.
  assertEquals(Outer.Inner.class.getNestHost().getSimpleName(), "Outer");

  String innerNestMembers = Arrays.stream(Outer.Inner.class.getNestMembers())
    .map(Class::getSimpleName)
    .collect(Collectors.joining(","));

  // Inner class 의 members 는 본인포함 + Host-Class 이다.
  assertEquals(Outer.Inner.class.getNestMembers().length, 2);
  assertEquals(innerNestMembers, "Outer,Inner");

  // 우리는 친구 (== nestmate 관계)
  assertTrue(Outer.class.isNestmateOf(Outer.Inner.class));
}
```

정리하면, 3가지 입니다.

- private field, method 는 **본인 && nestmate 관계일때 접근가능**으로 자바스펙이 변경됨
- nestmate 관계는 Outer/Inner 로 포함된 관계를 의미하고, 각각 Class\<?\> 레벨에서 메타데이타를 관리합니다.
  - Class.class.getNestMembers()
  - Class.class.getNestHost()
- 그에 따라 기존 bridge-method 를 생성해서 접근가능하게 처리했던 compiler 의 bytecode 삽입과정이 삭제됨
  - direct access 를 지원하므로

## 더 이상의 자세한 설명은 생략한다.

### [Dynamic Class-File Constants](http://openjdk.java.net/jeps/309)

### [Epsilon: A No-Op Garbage Collector](http://openjdk.java.net/jeps/318)

Garbage 를 수집하지않는 No-Op GC 를 사용가능

> -XX:+UnlockExperimentalVMOptions -XX:+UseEpsilonGC

즉 heap 이 꽉차면, GC 를 수행하지않고 OOM 으로 프로그램이 종료됨. 사용이 필요한 경우는

- GC 는 application 에서 제어할수 없고,
- 강제적으로 heap-full 을 만들고 싶어도 GC 가 동작한다.
  - 테스트 목적 등
- 그래서 명시적으로 (아무런 동작을 하지 않는) No-Op GC 를 사용하도록 선언

### [Unicode 10](http://openjdk.java.net/jeps/327)

Unicode API [10.0.0 스펙](http://www.unicode.org/standard/standard.html) 적용

- http://unicode.org/versions/Unicode10.0.0/

### [Flight Recorder](http://openjdk.java.net/jeps/328)

JVM metrics 수집하는 tool 이고, 유료 -> 무료로 전환됨.

```bash
# JAVA_OPTS
$ java -XX:+FlightRecorder -XX:StartFlightRecording=filename=dumpfile.jfr

# CLI tool
$ jcmd <pid> JFR.start
$ jcmd <pid> JFR.dump filename=dumpfile.jfr
$ jcmd <pid> JFR.stop
```

### [Transport Layer Security (TLS) 1.3](http://openjdk.java.net/jeps/332)

TLS 1.3 지원

### [A Scalable Low-Latency Garbage Collector (Experimental)](https://openjdk.java.net/jeps/333)

새로운 ZGC 가 탄생함. 동작원리는 ... 나중에 좀 어느정도 궤도에 올라가면 알아보자