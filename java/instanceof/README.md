# instanceof
```
https://jistol.github.io/java/2017/08/22/different-instanceof-isassignablefrom/
```

- instanceof
  - object
- assignable
  - class<?>

```java
@Test
public void primitiveAssignableTest() {
  Object obj = new Object();
  Class<?> clz = obj.getClass();

  if (obj instanceof String a) {
    System.out.println(a);
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