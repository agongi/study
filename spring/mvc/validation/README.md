## Validation

```
ㅁ Author: suktae.choi
ㅁ Reference:
- https://blog.outsider.ne.kr/825
- https://engkimbs.tistory.com/738
```

You need `validator` to verify input model via (ex. requestBody or ModelAttribute) into Controller.

First define custom validator:

```java
@Component
public class UserValidator implements Validator {

  @Override
  public boolean supports(Class<?> clazz) {
    return User.class.isAssignableFrom(clazz);
  }

  @Override
  public void validate(Object target, Errors errors) {
    User user = (User)target;
    
    ValidationUtils.rejectIfEmptyOrWhitespace(errors, "name", "name required");
    ValidationUtils.rejectIfEmpty(errors, "age", "age required");
  }
}
```

`BindingResult` is automatically created when the Controller params (ex. **RequestBody** or **ModelAttribute**) is binded.

```java
public void getUser(
  @RequestBody User user, 
  BindingResult bindingResult) {
  
	userValidator.validate(user, bindingResult);
  
  if (bindingResult.hasErrors()) {
  	throw new RuntimeException();
  }
}
```

or you can manually create it like this:

```java
public void getUser(@PathVariable String id) {
  User user = userRepository.findById(id);
  BindingResult bindingResult = new BeanPropertyBindingResult(user, "user");

  userValidator.validate(user, bindingResult);

  if (bindingResult.hasErrors()) {
    throw new RuntimeException();
  }
}
```

or using annotation to make it work

```properties
compile "org.hibernate:hibernate-validator:x.y.z.Final"
```

```java
public void getUser(
  @RequestBody @Valid User user, 
  BindingResult bindingResult) {
  
  if (bindingResult.hasErrors()) {
  	throw new RuntimeException();
  }
}

// User
public class User {
  @NotNull
  private String name;
  
  @Min(0)
  @Max(999)
  @NotNull
  private int age;
}
```

