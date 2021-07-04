## Java Generics

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.oracle.com/javase/tutorial/java/generics/index.html
- https://rangken.github.io/blog/2015/effective-java-4/
```

#### Index

- [Super type token](https://www.baeldung.com/java-super-type-tokens)
- [Type erase](type-erase)
- [\<T\> vs \<?\>](t-vs-question)
- [Invariant vs Covariant](invariant-vs-covariant)

***

### Type parameter

- Upper Bounded

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

- Lower Bounded

It doesn't supported

- Multiple Bounds

```java
Class A     { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }

<T extends A & B & C>  // ok, A is class
<T extends B & A & C>  // fail, B is interface
```

### Wildcards

- read: `Object`
- write: Not allowed except `null`

> write 는 미지원이지만 **[capture\<?\>helper](https://docs.oracle.com/javase/tutorial/java/generics/capture.html)** 를 이용해서 가능

```java
private void reverse(List<?> ids) {
  reverseHelper(ids);
}

private void reverseHelper(/* capture<?> */ List<T> ids) {
  for (...) {
    ids.add(i);	// List<?> is captured to List<T> and allowed to write
  }
}
```

#### Upper Bounded
```java
List<? extends Custom>
```

#### Lower Bounded
```java
List<? super Custom>
```

#### Multiple Bounds
It doesn't supported
