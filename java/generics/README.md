# Generics
```
https://docs.oracle.com/javase/tutorial/java/generics/index.html
https://rangken.github.io/blog/2015/effective-java-4/
```
### Index
- [Super type token](https://www.baeldung.com/java-super-type-tokens)
- [\<T\> vs \<?\>](t-question)
- [Invariant vs Covariant](invariant-covariant)
***

제네릭은 `컴파일 타임: 타입 체크 및 자동 캐스팅`을 제공하며, `런타임: 타입 정보가 제거`됩니다.

```java
List<String> list = new ArrayList<>();
list.add("Hello");

// 컴파일: 자동 캐스팅
String s = list.get(0);
=> String result = (String) list.get(0); // 자동 캐스팅

// 런타임: 타입 정보 제거
List<String> list = new ArrayList<>();
=> List list = new ArrayList<>();
```

## Type parameter
- read: `T`
- write: `T`

### Upper Bounded
```java
public class Box<T> {
  public <E extends Number> void inspect(E e) {
    // ...
  }

  public static void main(String[] args) {
    Box<Integer> box = new Box<Integer>();        
    box.inspect("some text");  // compile-error
  }
}
```

### Lower Bounded
```java
List<T super Custom>
```

## Wildcards
- read: `Object`
- write: Not allowed except `null`

> write 는 미지원이지만 **[capture\<?\>helper](https://docs.oracle.com/javase/tutorial/java/generics/capture.html)** 를 이용해서 가능

```java
private void reverse(List<?> ids) {
  add(ids);
}

private void add(/* capture<?> */ List<T> ids) {
  for (...) {
    ids.add(i);	// List<?> is captured to List<T> and allowed to write
  }
}
```

### Upper Bounded
```java
List<? extends Custom>
```

### Lower Bounded
```java
List<? super Custom>
```
