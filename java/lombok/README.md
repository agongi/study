## Lombok

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/49301670/does-lombok-builder-allow-extends
```

### Builder
#### Inheritance
```java
@AllArgsConstructor
public class Parent {
  private String a;
}

public class Child extends Parent {
  private String b;

  @Builder
  private Child(String a, String b) {
    super(a);
    b = b;
  }
}
```
