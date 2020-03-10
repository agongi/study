## InjectMocks Individually

```
ㅁ Author: suktae.choi
ㅁ References:
- https://medium.com/@flaviomata/using-reflectiontestutils-to-mock-autowired-methods-in-java-b97595a414a0
- https://howtodoinjava.com/spring-boot2/testing/testing-support/
```

### @InjectMocks

All mocks are injected annotated as @InjectMocks instance without any excuse.

```java
@SpringBootTest
public class userTest {
  @Mock UserRepository userRepository;
  @InjectMocks UserService userService = new UserServiceImpl();
  
  @Test
  public void test1() {
  	// userService injects repository  
  }
  
  @Test
  public void test1() {
    // userService injects repository
  }
}
```

### ReflectionTestUtils

You can programmatically control inject or not bean in target service.

> @InjectMocks annotation disappear

```java
@SpringBootTest
public class userTest {
  @Mock UserRepository userRepository;
  UserService userService = new UserServiceImpl();
  
  @Test
  public void test1() {
  	// userService injects repository
		ReflectionTestUtils.setField(userService, "userRepository", userRepository);
  }
  
  @Test
  public void test1() {
    // userService use bean
  }
}
```

