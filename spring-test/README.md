## Spring Test

```
ㅁ Author: suktae.choi
```

#### Index

- [JUnit](junit)
- [Mockito](mockito)

### Cores

#### Transaction

기본적으로 TestContext 프레임워크는 각 테스트마다 트랜잭션을 만들고 롤백한다. 트랜잭션 지원이 테스트의 어플리케이션 컨텍스트에서 정의된 PlatformTransactionManager 빈으로 테스트 클래스에 제공된다.

트랜잭션을 커밋하고 싶다면 @TransactionConfiguration와 @Rollback 어노테이션으로 트랜잭션을 롤백하는 대신에 커밋하도록 TestContext 프레임워크에 지시할 수 있다.

#### Annotation

**@ContextConfiguration**

applicationContext 가 올라가기 위해 필요한 Config.class 를 지정한다

```java
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
```

**@TestExecutionListeners**

Register listeners in `TestContext` should be invoked during test.

```java
@ActiveProfiles("alpha")
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

#### Blog

- [Why You Should Not Use InjectMocks](https://tedvinke.wordpress.com/2014/02/13/mockito-why-you-should-not-use-injectmocks-annotation-to-autowire-fields)

