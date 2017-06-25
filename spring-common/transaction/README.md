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
```

### Transaction
Transaction에서 익셉션 발생 시 기본적으로 롤백되는 것과 아닌 것이 있는데 무엇인가?
- default로 unchecked exception 인 RuntimeException 만 롤백
- checked 까지 롤백 하려면, @Transactional(rollbackFor = Exception.class)

Transaction에 여러 작업들 중 익셉션 발생 시 일부 작업은 커밋되도록 하는 방법은?
- noRollbackFor 으로 무조건 commit 되야 하는 항목을 설정한다
- NOT_SUPPORTED 로 설정해서 해당 항목은 무조건 commit 되게 한다
- REQUIRES_NEW 로 설정해서, 다른 작업과 무관한 Transaction 으로 처리한다
- @TransactionalEventListener(phase = TransactionPhase.AFTER_ROLLBACK)

#### @Transactional
- propagation
  - REQUIRED
  - SUPPORTS
  - MANDATORY
  - REQUIRES_NEW
  - NOT_SUPPORTED
  - NEVER
  - NESTED
- isolation
  - ISOLATION_DEFAULT - DB 에 설정된 값을 사용
- readOnly
- timeout
- rollbackFor
- rollbackForClassName
- noRollbackFor
- noRollbackForClassName

#### @TransactionalEventListener


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
