# JDK 21
```
https://www.freeblog-web.info/post/74?blogId=1
https://mangkyu.tistory.com/308
```

# JDK 18
## [Deprecate Finalization for Removal 그리고 finally](https://junhkang.com/posts/80/)
https://docs.oracle.com/javase/specs/jls/se21/html/jls-12.html#jls-12.6 의 명세를 보면
- Before the storage for an object is `reclaimed by the garbage collector`, the `Java Virtual Machine will invoke the finalizer` of that object.
  - The Java programming language `does not specify how soon a finalizer will be invoked`, except to say that it will happen before the storage for the object is reused. (호출되는 타이밍을 보장할 수 없음)
  - GC 의 발생 빈도가 증가합니다 (GC 가 실행될때 까지 리소스가 해제되지 않으므로)
  - GC 의 성능이 저하됩니다 (객체를 회수할때 finalize 를 호출하므로)
- The Java programming language imposes `no ordering on finalize method calls`. Finalizers may be called `in any order, or even concurrently`.
  - 다른 객체나 순서를 전제로 하는 코드를 작성하면 안됩니다
- If an uncaught exception is thrown `during the finalization, the exception is ignored` and finalization of that object terminates.
  - JVM GC 에 의해 호출되는 것이기 때문에, 예외가 발생해도 무시됩니다 (처리할 방법이 없음)

대안으로 `try-with-resources 및 AutoCloseable` 을 사용합니다.
- try-with-resources 구문이 종료될때 close() 메소드가 호출됩니다
  - 호출되는 타이밍을 보장할 수 있습니다
  - GC 의 발생 빈도를 줄일 수 있습니다
  - GC 의 성능이 저하되지 않습니다

# JDK 19 ~ 21
## [Pattern Matching for switch](https://medium.com/@jicholkim/pattern-matching-for-switch-0ec442268ec0)
```java
private String getAnimalSound(Animal animal) {
    // if (animal == null) throw new XYZException();
        
    return switch (animal) { // 기존 switch 는 animal null 일 때 NPE 발생
        case null -> "Animal does not exist";
        case Cat a -> a.sound(); // if (animal instanceof Cat a) { a.sound() } 처럼 타입 캐스팅 가능
        case Dog b -> b.sound();
        case Cow c -> c.sound();
        /**
         * case Chicken:
         * case Duck: {
         *    return "DDD";
         * }
         */
        case Chicken, Duck -> "DDD"; // 다수의 매칭 조건
        default -> "Unknown";
    };
}
```

## [Virtual Threads](../virtual-thread)
## [Sequenced Collections](https://www.baeldung.com/java-21-sequenced-collections)
각 자료구조마다 일관성 있는 방범 (add/get/removeFirst/Last 등) 을 정의하기 위해 `정렬되거나 입력순서로 element 를 저장하는 자료구조는 Sequenced... 를 상속`하도록 개선 되었습니다:

- 문제상황
<img width="75%" alt="image" src="1.png">

- 변경된 상속구조
<img width="50%" alt="image" src="2.png">
<img width="75%" alt="image" src="3.png">

- List (입력순서)
<img width="75%" alt="image" src="4.png">

- LinkedHashSet (입력순서) & TreeSet (정렬순서)
<img width="75%" alt="image" src="5.png">

- LinkedHashMap (맵의 KEY 가 입력순서)
<img width="75%" alt="image" src="6.png">

# JDK 22 ~ (or JDK 21 Preview)
[Unnamed Patterns and Variables (Preview)](https://www.freeblog-web.info/post/74?blogId=1)
[Unnamed Classes and Instance Main Methods (Preview)](https://www.freeblog-web.info/post/74?blogId=1)