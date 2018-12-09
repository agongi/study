## Mockito

```
ㅁ Author: suktae.choi
ㅁ References:
- http://static.javadoc.io/org.mockito/mockito-core/2.23.4/org/mockito/Mockito.html
- https://stackoverflow.com/questions/15976008/using-mockito-to-stub-and-execute-methods-for-testing
```

### Intro
#### @Mock
- `All methods` are stubbed. (Override)
- invoke method will do nothing unless it is specified

```java
// static method
Person p = Mockito.mock(Person.class);
// annotation
@Mock Person p;
```

#### @Spy
- `Only specified methods` are stubbed. (Overload)
- invoke method will do origin behavior unless it is specified

```java
// static method
Person p = Mockito.spy(Person.class);
// annotation
@Spy Person p;
```

#### @InjectMocks
DI @Mock/@Spy instances onto target.

```java
@Mock(name = "loginService")
private LoginService loginService;
@Spy
private LoginAuthUtils authUtils;
// loginFacade has loginService, authUtils inside and replaced with mocks
@InjectMocks
private LoginFacade loginFacade;
```

### Injected Strategy
#### MockitoAnnotations.initMocks(this);
```java
@RunWith(SpringJUnit4ClassRunner.class)
public class MockitoTestBase {
  @Mock private LoginService loginService;
  @InjectMocks private LoginFacade loginFacade;

  // explicit inject
  @Before
  public void before() {
    MockitoAnnotations.initMocks(this);
  }
}
```

#### MockitoJUnitRunner
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

#### MockitoRule
```java
// applicationContext is available
@RunWith(SpringJUnit4ClassRunner.class)
public class ExampleTest {
  // implicit inject
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
- given
- when
- then
