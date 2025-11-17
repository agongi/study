# Spring Test
### Blog
- [Why You Should Not Use InjectMocks](https://tedvinke.wordpress.com/2014/02/13/mockito-why-you-should-not-use-injectmocks-annotation-to-autowire-fields)
- [Why injecting by constructor should be preferred](http://pillopl.github.io/constructor-injection/)

***
## Transaction
기본적으로 TestContext 프레임워크는 각 테스트마다 트랜잭션을 만들고 롤백한다. 트랜잭션 지원이 테스트의 어플리케이션 컨텍스트에서 정의된 PlatformTransactionManager 빈으로 테스트 클래스에 제공된다.

트랜잭션을 커밋하고 싶다면 @TransactionConfiguration와 @Rollback 어노테이션으로 트랜잭션을 롤백하는 대신에 @Commit 을 통해 커밋하도록 TestContext 프레임워크에 지시할 수 있다.

## Dependency
- JUnit 이 기본 생성자를 이용해 테스트 객체를 생성 (JUnit 은 POJO 이므로 스프링관련 의존성 알수없음)
- 그 이후 TestContext 에서 `@Autowired/Setter` 를 이용해서 빈을 주입합니다

그러므로 생성자 주입방식은 동작하지 않으므로 아래와 같이 의존을 주입해야합니다:
```java
@RequiredArgsConstructor
public class AbcTest {
    @Autowired
    private AbcService abcService;
    private final XyzService xyzService; // 생성자주입은 되지않음 (JUnit 은 기본생성자로만 객체생성)
    
    // ...
}
```

## Annotation
### @ContextConfiguration
TestContext 프레임워크를 사용하는 테스트 클래스들은 어플리케이션 컨텍스트를 설정하기 위해 어떤 클래스도 상속받을 필요가 없고 특정 인터페이스를 구현할 필요도 없다.

대신 클래스 수준의 @ContextConfiguration 어노테이션을 선언함으로써 설정이 이뤄진다.

```java
@ContextConfiguration(classes=MyConfig.class)
public class MyTest {
	// methods..
}
```

### @RunWith
Spring JUnit integration. 

JUnit에 기반한 유닛테스트와 통합테스트를 구현할 수 있고 동시에 어플리케이션 컨텍스트 로딩, 테스트 인스턴스의 의존성 주입, 테스트 메서드 실행의 트랜잭션 적용 등의 TestContext 프레임워크의 이점을 얻을 수 있다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=MyConfig.class)
public class MyTest {
	// methods..
}
```

### @TestExecutionListeners
Register listeners in `TestContext` should be invoked during test.

```java
@TestExecutionListeners(CustomTestExecutionListener.class) 
@ContextConfiguration(classes=MyConfig.class)
public class MyTest {
	// methods..
}
```

And listener should implements this interface:

```java
public interface TestExecutionListener {
  void beforeTestClass(TestContext testContext) throws Exception;
  void prepareTestInstance(TestContext testContext) throws Exception;
  void beforeTestMethod(TestContext testContext) throws Exception;
  void afterTestMethod(TestContext testContext) throws Exception;
  void afterTestClass(TestContext testContext) throws Exception;
}
```

> This can be easily replaced in simple **@BeforeClass, @AfterClass** usages.

### @TransactionConfiguration
클래스 수준의 트랜잭션 설정 (ex. 트랜잭션 관리자와 기본 롤백 플래그에 빈 이름을 설정)

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration
@TransactionConfiguration(transactionManager="txMgr", defaultRollback=false)
@Transactional
public class FictitiousTransactionalTest {

  @BeforeTransaction
  public void verifyInitialDatabaseState() {
    // 트랜잭셩을 시작하기 전에 초기상태를 검증하는 로직
  }

  @Before
  public void setUpTestDataWithinTransaction() {
    // 트랜잭션내에서 테스트 데이터 구성
  }

  @Test
  // 클래스수준의 defaultRollback 설정을 오버라이드한다
  @Rollback(true)
  public void modifyDatabaseWithinTransaction() {
    // 테스트 데이터를 사용하고 데이터베이스의 상태를 수정하는 로직
  }

  @After
  public void tearDownWithinTransaction() {
    // 트랜잭션내의 "tear down" 로직을 실행한다
  }

  @AfterTransaction
  public void verifyFinalDatabaseState() {
    // 트랜잭션이 롤백된 후에 최종 상태를 검증하는 로직
  }
}
```

### @Rollback/@Commit
Transaction will be rollback by default. you can customize it.

You can use `@Commit` as a direct replacement for `@Rollback(false)` to more explicitly convey the intent of the code.

```java
@Rollback(false)
public void myTest() {
  // ...
}

@Commit
public void myTest() {

}
```

### @BeforeTransaction/@AfterTransaction
Declare methods should be executed in each tx:

```java
@BeforeTransaction 
void beforeTransaction() {
  // logic to be executed before a transaction is started
}

@AfterTransaction 
void afterTransaction() {
  // logic to be executed after a transaction has ended
}
```