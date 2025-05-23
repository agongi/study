# new T\[0\] vs new T\[size\]
```
https://www.baeldung.com/java-collection-toarray-methods
```

## Zero Initializations
[JLS-4.12.5](https://docs.oracle.com/javase/specs/jls/se21/html/jls-4.html#jls-4.12.5) 에서 `Each class variable, instance variable, or array component is initialized with a default value when it is created` 로 정의되어 있습니다.

이중에서 Array 도 선언시점에 기본값으로 초기화가 발생한다는 의미입니다. 그에 따라: 

- new T[0]
  - 사이즈 0: ZERO-PADDING 미발생
- new T[N]
  - 사이즈 N: ZERO-PADDING 발생

으로 불필요한 메모리점유 & 초기화 과정이 발생합니다.
