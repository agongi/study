## \<T\> vs \<?\>

```
@author: suktae.choi
- https://itexpertsconsultant.wordpress.com/2016/04/24/difference-between-list-liste-listobject-and-list-in-java/
```

### [\<T> vs \<?>](https://stackoverflow.com/questions/18176594/when-to-use-generic-methods-and-when-to-use-wild-card)

T 는 모두 동일하다가 보장되지만, ? 는 보장되지 않는다.

```java
// ensure dest, src represent exactly the same type
public static <T extends Number> void copy(List<T> dest, List<T> src)

// can't be ensured
public static void copy(List<? extends Number> dest, List<? extends Number> src)
```

대신 ? 는 type 을 알 필요 없을경우 (ex. Collection 자체에 관심이 있음) 사용해도 된다.

```java
public static <T> boolean isEmpty(List<T> list) {
  return list.size() < 1;
}

public static boolean isEmpty(List<?> list) {
  return list.size() < 1;
}
```

### Compared in Collection

#### Collection

Raw type 을 그대로 사용. compile 단계에서 type-check 가 전혀 되지 않으므로, runtime exception 발생

#### Collection\<Object>

아래의 Collection\<T\> 와 큰 차이는 없다. (type erase 되면 Object 로 치환되므로)

##### Collection\<T\>

상동

> During the type erasure process, the Java compiler erases all type parameters and replaces each with `its first bound` if the type parameter is bounded, or `Object` if the type parameter is unbounded.

#### Collection\<?>

wildcard 는 READONLY 로 취급된다. 즉 담겨진 타입의 get 은 가능하지만 set 은 불가능하다. (null 은 set 가능함)

