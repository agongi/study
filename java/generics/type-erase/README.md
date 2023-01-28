## Type erase

```
ㅁ Author: suktae.choi
- https://docs.oracle.com/javase/tutorial/java/generics/genTypes.html
```

우선 아래의 문장을 보면:

```markdown
During the type erasure process, the Java compiler erases all type parameters and replaces each with `its first bound` if the type parameter is bounded, or `Object` if the type parameter is unbounded.
```

`Type erase` 는 compile-time 때 타입안정성을 보장하기위해 JDK 1.5 에 도입된 기능이고, 기존 버전과의 호환을 위해 .class 에는 raw type (generic 삭제) 로 최종 결과물이 생성된다.

### Class

```java
public class Node<T> {

  private T data;
  private Node<T> next;

  public Node(T data, Node<T> next) {
    this.data = data;
    this.next = next;
  }

  public T getData() { return data; }
  // ...
}
```

```java
public class Node {			// <T> erased

  private Object data;	// T -> Object
  private Node next;		// <T> erased

  public Node(Object data, Node next) {
    this.data = data;
    this.next = next;
  }

  public Object getData() { return data; }
  // ...
}
```

