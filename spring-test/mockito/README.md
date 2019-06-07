## Mockito

```
ㅁ Author: suktae.choi
ㅁ References:
- http://static.javadoc.io/org.mockito/mockito-core/2.23.4/org/mockito/Mockito.html
- https://stackoverflow.com/questions/15976008/using-mockito-to-stub-and-execute-methods-for-testing
- https://jojoldu.tistory.com/226
- https://www.baeldung.com/java-spring-mockito-mock-mockbean
- https://dzone.com/articles/a-guide-to-mocking-with-mockito
```

### Inject mock
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

### EnableMockito

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
  @Rule
  public MockitoRule rule = MockitoJUnit.rule();

  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  @Test
  public void dummyTest() {
    System.out.println(loginService.getUser(123L));
  }
}
```

### Usage

#### Verify void method

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
    given(repository.findById(anyLong())).willReturn(Arrays.asList("001", "002"));

    // when - 기존에 이미 저장된 id 는 제거후, repository#save 호출
    service.create(Arrays.asList("002", "003"));

    // then
    verify(repository).save(idsCaptor.capture());   // save 직전의 argument 를 captor 한다.
    Assert.assertNotNull(idsCaptor.getValue());
    Assert.assertTrue(CollectionUtils.size(idsCaptor.getValue()) == 1); // "003"
  }
}
```