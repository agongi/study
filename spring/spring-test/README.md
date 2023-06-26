# Spring Test

```
@author: suktae.choi
```

### Index

- [JUnit](junit)
- [Mockito](mockito)
- [Smoke test vs Sanity test](smoke-sanity)
- [Set final field](set-final-field)

### Blog

- [Why You Should Not Use InjectMocks](https://tedvinke.wordpress.com/2014/02/13/mockito-why-you-should-not-use-injectmocks-annotation-to-autowire-fields)
- [Why injecting by constructor should be preferred](http://pillopl.github.io/constructor-injection/)

***

## Transaction

기본적으로 TestContext 프레임워크는 각 테스트마다 트랜잭션을 만들고 롤백한다. 트랜잭션 지원이 테스트의 어플리케이션 컨텍스트에서 정의된 PlatformTransactionManager 빈으로 테스트 클래스에 제공된다.

트랜잭션을 커밋하고 싶다면 @TransactionConfiguration와 @Rollback 어노테이션으로 트랜잭션을 롤백하는 대신에 커밋하도록 TestContext 프레임워크에 지시할 수 있다.

## Dependency

TestContext 프레임워크는 테스트 인스턴스를 인스턴스하는 방법으로 구성하지 않는다. 그러므로 생성자에 @Autowired나 @Inject를 사용해서 테스트 클래스에는 효과가 없다.

> Field injection or setter is working

## Annotation

**@ContextConfiguration**

TestContext 프레임워크를 사용하는 테스트 클래스들은 어플리케이션 컨텍스트를 설정하기 위해 어떤 클래스도 상속받을 필요가 없고 특정 인터페이스를 구현할 필요도 없다. 대신 클래스 수준의 @ContextConfiguration 어노테이션을 선언함으로써 설정이 이뤄진다.

```java
@ContextConfiguration(classes=MyConfig.class)
public class MyTest {
	// methods..
}
```

**@RunWith**

Spring JUnit integration. 

JUnit에 기반한 유닛테스트와 통합테스트를 구현할 수 있고 동시에 어플리케이션 컨텍스트 로딩, 테스트 인스턴스의 의존성 주입, 테스트 메서드 실행의 트랜잭션 적용 등의 TestContext 프레임워크의 이점을 얻을 수 있다.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=MyConfig.class)
public class MyTest {
	// methods..
}
```

**@ActiveProfiles**

Phase 정의

```java
@ActiveProfiles("alpha")
@ContextConfiguration(classes=MyConfig.class)
public class MyTest {
	// methods..
}

@Profile("alpha")
@Configuration
public class ListenerConfig {
  @Bean
  public TestExecutionListener listener() {
    return new CustomTestExecutionListener("alpha");
  }
}
```

**@TestExecutionListeners**

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

**@TransactionConfiguration**

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

**@Rollback/@Commit**

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

**@BeforeTransaction/@AfterTransaction**

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

**@Sql**

`@Sql` is used to annotate a test class or test method to configure SQL scripts to be run against a given database during integration tests. The following example shows how to use it:

```java
@Test
@Sql({"/test-schema.sql", "/test-user-data.sql"}) 
public void userTest {
  // execute code that relies on the test schema and test data
}
```

**@Timed**

It indicates that this annotated method must finish execution in a specific period (milliseconds).

```java
@Test
@Timed(millis = 1000)
public void timeOutTest() {
  // should not take longer than 1 sec
}
```

**@Repeat**

Annotated method repeatly being executed.

```java
@Test
@Repeat(10) 
public void testProcessRepeatedly() {
  // 10 times executed
}
```