## Mockito

```
ㅁ Author: suktae.choi
ㅁ References:
- http://static.javadoc.io/org.mockito/mockito-core/2.23.4/org/mockito/Mockito.html
- https://stackoverflow.com/questions/15976008/using-mockito-to-stub-and-execute-methods-for-testing
- https://jojoldu.tistory.com/226
- https://www.baeldung.com/java-spring-mockito-mock-mockbean
- https://dzone.com/articles/a-guide-to-mocking-with-mockito
- https://juneyr.dev/2019-02-08/mockito-repo-save
```

`Mockito` is a framework for unit-test in java that mocking tastable fields or variables.

- given: **when**(mock#method).thenReturn(T)
- when: service invoke
- then
  - **verify**(mock).method(?, ?)
  - **verify**(mock, times(N)).method(?, ?)

`MockitoBDD` (Behavior-driven development) tests in a natural, human-readable language that focuses on the behavior of the application.

- given: **given**(mock#method).willReturn(T)
- when: service invoke
- then
  - **then**(mock).should().methodI(?, ?)
  - **then**(mock).should(times(N)).method(?, ?)

### Enable

#### MockitoAnnotations.initMocks(this);

Enable mockito framework in code.

- Not running with applicationContext but pure-java

```java
@RunWith(SpringJUnit4ClassRunner.class)
public class MockitoTestBase {
  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  @Before
  public void before() {
    // explicit inject
    MockitoAnnotations.initMocks(this);
  }
}
```

#### @RunWith(MockitoJUnitRunner.class)

Enable mockito framework over annotation.

- Not running with applicationContext but pure-java

```java
// implicit inject
@RunWith(MockitoJUnitRunner.class)
public class MockitoTestBase {
  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;
  
  // springRunner is not used. applicationContext is not available
  @Autowire private LoginService loginService;
}
```

#### MockitoJUnit.rule()

Enable mockito with springContext

- Running with applicationContext

```java
// spring + junit
@RunWith(SpringJUnit4ClassRunner.class)
public class ExampleTest {
  // with mockito
  @Rule public MockitoRule rule = MockitoJUnit.rule();

  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  @Test
  public void dummyTest() {
    System.out.println(loginService.getUser(123L));
  }
}
```

### Annotation

#### @Mock
- `All methods` are stubbed. (Override)
- invoke method will do nothing unless it is specified

```java
public class PersonService {
  // ... fields
  public Integer getAge() { ... }
  public String getName() { ... }
}

// static method
PersonService p = Mockito.mock(PersonService.class);
// annotation
@Mock private PersonService p;

// given
given(p.getAge()).willReturn(100);

// when
Integer age = p.getAge();
String name = p.getName();

// then
Assert.assertEquals(age, 100);
Assert.assertNull(name);	// return nothing (not defined)
```

#### @Spy
- `Only specified methods` are stubbed. (Overload)
- invoke method will do origin behavior unless it is specified

```java
public class PersonService {
  // ... fields
  public Integer getAge() { ... }
  public String getName() { ... }
}

// static method
PersonService p = Mockito.spy(PersonService.class);
// annotation
@Spy private PersonService p = new PersonServiceImpl();

// given
given(p.getAge()).willReturn(100);

// when
Integer age = p.getAge();
String name = p.getName();

// then
Assert.assertEquals(age, 100);
Assert.assertNotNull(name);	// return service result
```

#### @InjectMocks
DI @Mock/@Spy instances onto target.

```java
@Mock(name = "loginService")
private LoginService loginService;
@Spy
private LoginAuthUtils authUtils = new LoginAuthUtils();

// @Mock & @Spy will be injected into @InjectMocks target
@InjectMocks
private LoginFacade loginFacade = new LoginFacade();
```

> Spring-boot provides @MockBean and @SpyBean as well. The main difference is these are contained in applicationContext as bean (not POJO).

### Usage

#### Method argument captor

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
    verify(repository).save(idsCaptor.capture());   // save 직전의 argument 를 captor 한다.
    Assert.assertNotNull(idsCaptor.getValue());
    Assert.assertTrue(CollectionUtils.size(idsCaptor.getValue()) == 1); // "003"
  }
}
```

#### Method returns mock

```java
// testable
public List<Object> getSites(Long id) {
  User user = repository.findById(id);

  List<Site> sites = user.getLoginableSites();
  // .... 
  return sites;
}

// test
public class CrudTest {
  @Mock
  private CRUDRepository repository;
  @InjectMocks
  private UserService userService = new UserServiceImpl();

  @Test
  public void test() {
    // given
    Long id = 123456L;
    User user = mock(User.class);	// or @Mock private User user;
    given(user.getLoginableSites()).willReturn(Arrays.asList(
      new Site(1),
      new Site(2),
      new Site(3)
    ));
    
    given(repository.findById(anyLong()))
      .willReturn(user);

    // when
    List<Site> sites = userService.getSites(id);

    // then
    assertNotNull(sites);
    assertTrue(sites.size(), 3);
  }
}
```

#### Method returns arguments

```java
given(userRepository.save(any(User.class)))
  .will(AdditionalAnswers.returnsFirstArg());
```

#### Method skip (Only void methods can doNothing())

```java
PowerMockito.doNothing()
  .when(validationService)
  .check(anyLong(), any(ValidationType.class));
```

#### Any argument

```java
#method(any(Class.class));	// Class<?>
#method(any(UserType.class));	// Enum<?>
#method(anyObject());	// Instance
```

#### Static class mocking

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

#### Method invocation

```java
// then - BDDMockito
then(userService).should().joinSite(anyLong(), anyBoolean()); // invoked
then(userService).should(times(3)).joinSite(anyLong(), anyBoolean()); // 3-times invoked
then(userService).should(never()).joinSite(anyLong(), anyBoolean());  // never invoked
```

