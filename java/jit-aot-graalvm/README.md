# JIT vs AOT

```
https://www.oracle.com/technical-resources/articles/java/architect-evans-pt1.html
https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm
https://kotlinworld.com/307?category=914495
```

## Just-in-time Compiler
<img src="1.png" width="50%">

핫스팟 JVM 은 C1, C2 2가지 유형의 JIT Compiler 가 존재합니다:

- C1 compiler (== -client)
  - -client 컴파일러
  - 코드 최적화는 덜하지만 즉시 시작되는 속도는 빠름
- C2 compiler
  - -server 컴파일러
  - 즉시 시작되는 속도는 느리지만 최적화는 많이 되어 warm-up 후에는 빠름

### CodeCache
C2 로 컴파일된 코드는 CodeCache 에 저장 & interpret 시점에 (interpret 결과) 를 캐시에서 가져옵니다

<img src="2.png" width="75%">

한번 저장된 code 는 아래 케이스일때 삭제됩니다.

- Optimization failed -> re-calculate
- Compiler changed
- 메서드를 지닌 `클래스 언로딩`

마찬가지로 CodeCache 도 단편화가 발생 할 수 있습니다.

## Ahead-Of-Time Compiler

## GraalVM
C2 컴파일러를 대체할 수 있는 새로운 컴파일러
