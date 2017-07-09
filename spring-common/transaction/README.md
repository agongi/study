## Transaction

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.23
ㅁ References:
  - http://haviyj.tistory.com/33
  - https://blog.outsider.ne.kr/869
  - http://lng1982.tistory.com/128
  - http://javacan.tistory.com/entry/Handle-DomainEvent-with-Spring-ApplicationEventPublisher-EventListener-TransactionalEventListener
  - https://spring.io/blog/2015/02/11/better-application-events-in-spring-framework-4-2
  - http://springsource.tistory.com/136
```

### Transaction
#### @Transactional
- propagation
  - **REQUIRED** - join existing, create new if no

  <img src="https://github.com/agongi/study/blob/master/spring-common/transaction/images/x1134407086.gif.pagespeed.ic.NDodWWj_K8.png">

  - REQUIRES_NEW - create new always, independent to existing

  <img src="https://github.com/agongi/study/blob/master/spring-common/transaction/images/x1025204939.gif.pagespeed.ic.qc3nIvzXgN.png">

  - NESTED - join existing, dependent to existing
    - Outer can affect nested, nested will not affect outer (e.g. Outer - Critical, Nested - Logging)
  - SUPPORTS - join existing, no if no
  - MANDATORY - join existing, throw exception if no
  - NOT_SUPPORTED - ignore if exist
  - NEVER - throw exception if exist

- isolation
  - **DEFAULT** - Use the DB setting (e.g. DB sets READ_COMMITTED then use it)
  - READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE
- readOnly
- timeout
- rollbackFor
- rollbackForClassName
- noRollbackFor
- noRollbackForClassName

#### Default Rollback Plan
- (Default) RuntimeException only causes rollback subclass of unchecked exception
- Customize - **@Transactional(rollbackFor = Exception.class)** to rollback all cases

#### Rollback specific commit while exception thrown
- REQUIRES_NEW 로 설정해서, 다른 작업과 무관한 Transaction 으로 처리한다
- NESTED 로 설정해서, Outer 에 영향을 끼치지 않는 트랜잭션으로 분리 (대신 Outer 은 Nested 에 영향을 끼침)
- @TransactionalEventListener(phase = TransactionPhase.AFTER_ROLLBACK)

#### @TransactionalEventListener
TBD

#### Annotation
```xml
<tx:annotation-driven proxy-target-class="true" />
```
```java
<tx:attributes>
	<tx:method name="get*" read-only="true" />
	<tx:method name="*" />
</tx:attributes>

=================================================

@Transactional
public class UserService {
    void add(User user) {}
    void deleteAll() {}
    void update(User user) {}
    void upgradeLevels() {}

    @Transactional(readOnly = true)
    User get(String id) {}

    @Transactional(readOnly = true)
    List<User> getAll() {}
}
```

#### Miscellaneous
- @Rollback - Rollback after test method has completed
- @Commit - Commit after test method has completed
