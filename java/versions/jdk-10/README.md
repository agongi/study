## JDK 10

```
ㅁ Author: suktae.choi
- https://itstory.tk/entry/Java-10-%EC%8B%A0%EA%B7%9C-%EA%B8%B0%EB%8A%A5%ED%8A%B9%EC%A7%95-%EC%A0%95%EB%A6%AC
- https://shipilev.net/jvm/anatomy-quarks/22-safepoint-polls/
```

JDK 10 - http://openjdk.java.net/projects/jdk/10/

***

## [Local-Variable Type Inference](http://openjdk.java.net/jeps/286)

지역변수 선언시 타입추론된 `var` 를 사용 가능합니다.

```java
// local-variable
var x = new Foo();
// iteration
for (var x : xs) { ... }
// try-with-resource
try (var x = ...) { ... } catch ...
```

대신 Generic 타입추론이 안되므로, 선언시 \<\> Generic Type 은 명시해야합니다.

> 미지정시 Object 로 관리됨

```java
// Generic type-infer
List<String> users = new ArrayList<>();

// type-infer 못하므로, users 는 List<Object> 로 취급
var users = new ArrayList<>();

// 직접 명시해야함.
var users = new ArrayList<String>();
```

## 더 이상의 자세한 설명은 생략한다.

### [Consolidate the JDK Forest into a Single Repository](http://openjdk.java.net/jeps/296)

### [Garbage-Collector Interface](http://openjdk.java.net/jeps/304)

GC 인터페이스를 만들어서 서로 다른 구현체들의 코드를 분리 할수있게 개선

> ~~핀포인트 파이팅~~

### [Parallel Full GC for G1](http://openjdk.java.net/jeps/307)

Young/Old 구분없는 G1 은 full-gc 빈도가 낮지만, 발생시 single-thread 로 mark-sweep 을 처리해서 STW 시간이 길었습니다.

이제는 Parallel 로 mark-sweep-compact 를 처리가능합니다. (Young, Mixed GC 모두 동일하게 적용되는 병렬처리 값)

> -XX:ParallelGCThreads

### [Application Class-Data Sharing](http://openjdk.java.net/jeps/310)

### [Thread-Local Handshakes](http://openjdk.java.net/jeps/312)

JVM safepoint 는 GC 수행전 모든 쓰레드의 정지를 기다리는 메카니즘입니다.

대신 아래의 비효율이 있음:

- thread 는 `reasonable interval` 로 safepoint flag 를 polling 해야함
  - method entry/exit
  - 2 bytecodes execution
  - infinite loop execution
- 그래서 GC 가 필요해서, safepoint flag = true; 설정시, 각각의 개별 쓰레드는 polling 하다가 산발적으로 스탑됨
- flag = false; 로 지정해도 즉시 thread stop 이 풀리지않고, 각 개별 쓰레드가 polling 하면서 산발적으로 재시작됨
  - 이때는 `time-based interval` polling

polling 에 대한 효율적인 개선방법으로 아래의 handshake-callback 으로 Flow 를 변경 가능합니다:

- thread 와 handshakes 를 통해, 콜백을 줄수있도록 세팅함
- safepoint 가 필요한 시점에 callback 으로 thread-local::safepoint = true; 로 세팅
- 개별 thread 는 JVM safepoint flag 를 보지않고, thread-local flag 를 확인
- 처리가 끝난후, 역시 callback 을통해 thread-local::safepoint = false; 세팅

> -XX:ThreadLocalHandshakes (x64-SPARC 플랫폼에서 선제적용)

2가지 지점에서의 튜닝

- JVM global flag 를 보지않고, thread-local 을 확인. 해당값은 CPU registry 에 저장되어 L1 캐시같이 빠른 처리가능
- 각 개별 thread 만 선별적으로 stop 시그널 줄수있는 mechanism 을 구축해놓음
  - 기존방식은 all or nothing 방식 (global flag)

더 명확하게 확인하려고, JIRA proposal 의 명시된 [openjdk code](https://github.com/openjdk/jdk/blob/master/src/hotspot/cpu/sparc/macroAssembler_sparc.cpp#L238) 를 체크

```cpp
void MacroAssembler::safepoint_poll(Label& slow_path, bool a, Register thread_reg, Register temp_reg) {
  if (SafepointMechanism::uses_thread_local_poll()) {
    ldx(Address(thread_reg, Thread::polling_page_offset()), temp_reg, 0);
    // Armed page has poll bit set.
    and3(temp_reg, SafepointMechanism::poll_bit(), temp_reg);
    br_notnull(temp_reg, a, Assembler::pn, slow_path);
  } else {
    AddressLiteral sync_state(SafepointSynchronize::address_of_state());

    load_contents(sync_state, temp_reg);
    cmp(temp_reg, SafepointSynchronize::_not_synchronized);
    br(Assembler::notEqual, a, Assembler::pn, slow_path);
  }
}
```

- uses_thread_local_poll() 로 적용유무확인
  - true -> thread-local value 확인
  - false -> 기존방식으로 처리

### [Remove the Native-Header Generation Tool (javah)](http://openjdk.java.net/jeps/313)

### [Additional Unicode Language-Tag Extensions](http://openjdk.java.net/jeps/314)

### [Heap Allocation on Alternative Memory Devices](http://openjdk.java.net/jeps/316)

### [Experimental Java-Based JIT Compiler](http://openjdk.java.net/jeps/317)

Graal JIT Compiler 을 사용가능함 (c1, c2 에 영향이 가겠지)

> -XX:+UnlockExperimentalVMOptions -XX:+UseJVMCICompiler

### [Root Certificates](http://openjdk.java.net/jeps/319)

root cert 를 갱신함.

> {JAVA_HOME}/lib/security/cacerts 파일

### [Time-Based Release Versioning](http://openjdk.java.net/jeps/322)