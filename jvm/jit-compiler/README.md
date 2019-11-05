## JIT (Just-In Time) Compiler

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.oracle.com/technical-resources/articles/java/architect-evans-pt1.html
- https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm
```

### Overview

핫스팟 JVM 에는 C1, C2 라는 2가지 유형의 JIT Compiler 가 존재합니다.

- C1: -client
- C2: -server

2가지 컴파일러 모두 핵심 측정값 (metric), 즉 `메서드 호출횟수` 에 따라 컴파일이 트리거링 됩니다. 

컴파일시 같은 코드라도 C1, C2 의 결과는 상이한데, C1 은 컴파일 시간이 짧고 단순한 대신 C2 처럼 정교한 최적화는 수행하지 않는다.

- C1
  - Compiled result: simple
  - Compilation tile: short
  - How much optimized: low
- C2
  - Compiled result: complicated
  - Compilation tile: long
  - How much optimized: high

#### Life-cycle

Hotspot 은 프로파일링 정보를 저장하지 않고, VM 이 내려가면 삭제합니다.

> 즉  VM 최초 실행시는 profile + compile -> codeCache 저장의 과정이 필요하다.

#### Tiered compilation

JDK 6 이후부터 단계적 컴파일이 적용었는데, 의미는 다음과 같다:

> bytecode -> Interpret -> C1 compiled -> C2 compiled

정확한 실행레벨은 아래와 같다:

- Level 0: Interpret
- Level 1: C1 - full optimization
- Level 2: C1 - invocation counter + backedge counter (무슨 의미인지..)
- Level 3: C1 - full profiling
- Level 4: C2

모든 레벨을 순차적으로 거치는게 아니라, 코드의 유형에 따라 적절히 선택한다.

```json
## 단순 method 의 경우
- level0: interpret
- level3: full-profiling (단순 메서드인지 판단)
- level4: c2 적용

level3 단계에서 단순메서드는 특별히 할일이 없고, C1 은 C2 보다 최적화 할 수 없으므로 바로 level4 로 넘김
```

#### CodeCache

JIT 으로 컴파일된 코드는 CodeCache 에 저장됩니다. 

CodeCache 가 full occupied 상태라면, 더이상의 method 는 컴파일되지 않고 interpret 로만 동작하게 됩니다.

> Performance impact

한번 저장된 code 는 아래 케이스일때 삭제됩니다:

- Optimization failed -> re-calculate
- Compiler changed
- 메서드를 지닌 `클래스 언로딩`

> 스프링에서 bean 은 기본적으로 싱글톤 이기 때문에, 클래스가 내려가지 않음. 즉 항상 점유하는 상태가 유지

마찬가지로 CodeCache 도 단편화가 발생 할 수 있습니다. (== Need compaction)