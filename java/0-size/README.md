## new T\[0\] vs new T\[size\]

```
ㅁ Author: suktae.choi
- https://www.baeldung.com/java-collection-toarray-methods
```

## Zero Initializations

[JLS-4.12.5](https://docs.oracle.com/javase/specs/jls/se8/html/jls-4.html#jls-4.12.5) 에 의해 

- class variable
- instance variable
- array

는 zero-initialization 이 발생합니다. 즉 변수의 선언과 동시에 기본값으로 초기화가 발생한다는 의미이고, 그 과정에서의

- new T[0]
  - pre-zeroing 미발생
- new T[size]
  - array.legnth 만큼 element 가 null or 0 로 초기화 필요

의 차이로 인해 성능저하가 발생합니다.

내부적으로 코드를 보면

```java
Object[] src = this.src;
Foo[] dst = new Foo[size];
System.arraycopy(src, 0, dst, 0, src.length);
```

으로 `System.arraycorpy` 가 호출되는것은 동일하지만 해당 코드의 구현은 native 로 JNI 구현입니다.

즉 내부 호출되는 부분이므로 최적화부분이 존재하고, 해당 로직은 JLS 과는 상관없이 C 구현체이므로 (or others) zeroing 등의 이슈와 무관합니다. 그러므로 pre-allocated array or empty array 에서 JLS 스펙에 따라

- pre-zeroing 

이 발생하는 new T\[size\] 가 불필요한 초기화가 발생하므로 low-performance 입니다.

