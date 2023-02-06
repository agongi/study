## Mockito

```
@author: suktae.choi
- http://static.javadoc.io/org.mockito/mockito-core/2.23.4/org/mockito/Mockito.html
- https://stackoverflow.com/questions/15976008/using-mockito-to-stub-and-execute-methods-for-testing
- https://jojoldu.tistory.com/226
- https://www.baeldung.com/java-spring-mockito-mock-mockbean
- https://dzone.com/articles/a-guide-to-mocking-with-mockito
- https://juneyr.dev/2019-02-08/mockito-repo-save
```

#### Index

- [InjectMocks](injectmocks)

***

### Concepts

`Mockito` is a framework for unit-test in java that mocking tastable fields or variables.

- given
  - **when**(mock#method).thenReturn(T)
- when
  - service invoke
- then
  - **verify**(mock).method(?, ?)
  - **verify**(mock, times(N)).method(?, ?)

`MockitoBDD` (Behavior-driven development) tests in a natural, human-readable language that focuses on the behavior.

- given
  - **given**(mock#method).willReturn(T)
- when
  - service invoke
- then
  - **then**(mock).should().methodI(?, ?)
  - **then**(mock).should(times(N)).method(?, ?)

### Setup

#### Pure Java

```java
// run mockito
@RunWith(MockitoJUnitRunner.class)
public class MockitoTestBase {
  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  // springRunner is not used. applicationContext is not available
  @Autowire private LoginService loginService;
}
```

#### Spring integration

- bootstrap in init

```java
@RunWith(SpringJUnit4ClassRunner.class)
public class MockitoTestBase {
  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  @Before
  public void before() {
    // bootstrap
    MockitoAnnotations.initMocks(this);
  }

  @Test
  public void dummyTest() {
    System.out.println(loginService.getUser(123L));
  }
}
```

- bootstrap in annotation

```java
@RunWith(SpringJUnit4ClassRunner.class)
public class ExampleTest {
  // bootstrap
  @Rule public MockitoRule rule = MockitoJUnit.rule();
  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  @Test
  public void dummyTest() {
    System.out.println(loginService.getUser(123L));
  }
}
```

### Annotations

#### @Mock
- `All methods` are stubbed. (Override)
- invoke method will do nothing unless it is specified

```java
private static class PersonService {
  // ... fields
  public Integer getAge() { ... }
  public String getName() { ... }	
}

public class TestJunit {
  // annotation define
  @Mock private PersonService p;

  @BeforeEach
  public void before() {
    // code define
    PersonService p = Mockito.mock(PersonService.class);  
  }

  public void test() {
    // given
    given(p.getAge())
      .willReturn(100);

    // when
    Integer age = p.getAge();
    String name = p.getName();	// do nothing

    // then
    Assert.assertEquals(age, 100);
    Assert.assertNull(name);
  }
}
```

#### @Spy
- `Only specified methods` are stubbed. (Overload)
- invoke method will do origin behavior unless it is specified

```java
private static class PersonService {
  // ... fields
  public Integer getAge() { ... }
  public String getName() { ... }	
}

public class TestJunit {
  // annotation define
  @Spy private PersonService p = new PersonServiceImpl();

  @BeforeEach
  public void before() {
    // code define
    PersonService p = Mockito.spy(PersonService.class);
  }

  public void test() {
    // given
    given(p.getAge())
      .willReturn(100);

    // when
    Integer age = p.getAge();
    String name = p.getName();	// do something

    // then
    Assert.assertEquals(age, 100);
    Assert.assertNull(name);
  }
}
```

#### @InjectMocks
DI @Mock/@Spy instances onto target.

```java
@Mock(name = "loginService") private LoginService loginService;
@Spy private LoginAuthUtils authUtils = new LoginAuthUtils();

// @Mock & @Spy will be injected into @InjectMocks target
@InjectMocks
private LoginFacade loginFacade = new LoginFacade();
```

#### @MockBean/@SpyBean (supported spring-boot)

@Mock/@Spy 는 TestCode 에서 직접 생성한 객체를 @injectMock 을 통해 주입하는 방식이라면,

@MockBean/@SpyBean 은 applicationContext 의 빈을 직접 변경하는 방식이다.

> You can use the annotation to add new beans or replace a single existing bean definition

```java
static class UserFacade {
  @Autowire private UserService userService;
  @Autowire private UserRepository userRepository;
  
  public void login(String userId) {
    User user = userRepository.findById(userId);
    if (user == null) {
      throw new UserNotFound();
    }
    
    return userService.login(user);
  }
}

public class TestJunit {
  @MockBean("userService") private UserService userService;
  @MockBean("userRepository") private UserRepository userRepository;
  // @InjectMocks 을 통한 DI 없음
  @Autowire private UserFacade userFacade;
  
  public void userFacadeTest() {
    // given
    given(userService.login(anyString()))
      .willReturn(new User("test-id"));
    
    // when
    User user = userFacade.login("test-id");
    // then
    assertNotNull(user.getId());
  }
}
```

### References

#### Given

- Retures user-defined

```java
given(repository.findById(anyLong()))
  .willReturn(new User());
```

- Returns passed argument

```java
given(userRepository.save(any(User.class)))
  .will(AdditionalAnswers.returnsFirstArg());
```

#### When

- Captor argument

```java
public class CrudTest {
  @Mock
  private CrudRepository repository;
  @Captor
  private ArgumentCaptor<List<Long>> idsCaptor;
  @InjectMocks
  private CrudService service = new CrudServiceImpl();

  @Test
  public void CRUD_TEST() {
    // given
    given(repository.findById(anyLong()))
      .willReturn(Arrays.asList("001", "002"));

    // when
    service.create(Arrays.asList("002", "003"));

    // then
    //then(repository).should().save(idsCaptor.capture());	// save 직전의 argument 를 captor 한다.
    verify(repository).save(idsCaptor.capture());   	// save 직전의 argument 를 captor 한다.
    Assert.assertNotNull(idsCaptor.getValue());
    Assert.assertTrue(CollectionUtils.size(idsCaptor.getValue()) == 1); // "003"
  }
}
```

- Skip `void` method

```java
PowerMockito.doNothing()
  .when(validationService)
  .check(anyLong(), any(ValidationType.class));
```

> 기본적으로 Mock 은 별도 given 으로 지정하지 않으면 method 는 do nothing 이다.

#### Then

- Invocation confirmed
```java
// then - BDDMockito
then(userService).should().joinSite(anyLong(), anyBoolean()); // invoked
then(userService).should(times(3)).joinSite(anyLong(), anyBoolean()); // 3-times invoked
then(userService).should(never()).joinSite(anyLong(), anyBoolean());  // never invoked
```

#### Others

- Any argument

```java
#method(any(Class.class));	// Class<?>
#method(any(UserType.class));	// Enum<?>
#method(anyObject());	// Instance
```

- Static Class

```java
// EnablePowerMock
@RunWith(PowerMockRunner.class)
@PrepareForTest(StaticBeanProvider.class)
public class TestClass {
  @Mock private MockService mockService;

  @Test
  public void test() {
    // static class mock
    PowerMockito.mockStatic(StaticBeanProvider.class);

    // given
    given(StaticBeanProvider.getBean(MockService.class))
      .willReturn(mockService);

    given(mockService.findOne(anyLong())
      .willReturn(new User("testName"));
  }
}
```

