## Set final field

```
@author: suktae.choi
- https://stackoverflow.com/questions/51521837/spring-reflectiontestutils-does-not-set-static-final-field
```

```java
@Test
public void userLoginTest() {
  Field field = ReflectionTestUtils.getField(PhaseResolver.class, "PHASE");

  // modify final field
  setFinalStatic(field, "beta");
}

// reflect static final field for test
private static void setFinalStatic(Field field, Object newValue) throws Exception {
  Field modifiersField = Field.class.getDeclaredField("modifiers");
  ReflectionUtils.makeAccessible(field);
  ReflectionUtils.makeAccessible(modifiersField);

  modifiersField.setInt(field, field.getModifiers() & ~Modifier.FINAL);
  field.set(null, newValue);
}
```

