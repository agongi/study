## instanceof

```
ㅁ Author: suktae.choi
- https://jistol.github.io/java/2017/08/22/different-instanceof-isassignablefrom/
```

- instanceof
  - object
- assignable
  - class<?>

### Primitives type-check

```java
@Test
public void primitiveAssignableTest() {
  Object obj = "";
  Class<?> clz = obj.getClass();

  if (obj instanceof String) {
    // ...
  }

  // compile-time failed
  if (obj instanceof clz) {
		// Class<?> is not clear
  }

  // Class#isAssignableFrom
  if (String.class.isAssignableFrom(clz)) {
    // ...
  }

  // ClassUtils#isAssignable
  if (ClassUtils.isAssignable(clz, String.class)) {
    // ...
  }
}
```

### Collections type-check
```java
@Test
public void collectionAssignableTest() {
  Object obj = Collections.singletonList("");
  Class<?> clz = obj.getClass();

  if (obj instanceof Collection) {
    // ...
  }

  // compile-time failed
  if (obj instanceof clz) {
		// Class<?> is not clear
  }

  // Class#isAssignableFrom
  if (Collection.class.isAssignableFrom(clz)) {
    // ...
  }

  // ClassUtils#isAssignable
  if (ClassUtils.isAssignable(clz, Collection.class)) {
    // ...
  }
}
```
