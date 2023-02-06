## Object-Oriented Query Language

```
@author: suktae.choi
- http://ithub.tistory.com/27
```

### Spring Data JPA
```java
// Spring will generate Impl class
public interface UserRepository extends JpaRepository<User, Long> {
  // method name queries
}
```

### JPQL (JPA Query Language)
- 데이터베이스 테이블을 대상으로하는 데이터 중심의 쿼리가 아닌 객체를 대상으로 검색하는 객체지향 쿼리
- SQL을 추상화해서 특정 데이터베이스 SQL에 의존하지 않는다
- JPQL에서는 별칭을 필수로 작성 해야된다

```java
// JPQL - text, non-typeSafety, static
String jpql = "select m from Member as m where m.student_number = '0991022'";
Member member = entityManager.createQuery(jpql, Member.class).getMember();
```

> 쉽게 사용하기위한 Builder Patterns

- Criteria
  - JPQL builder complexed

  ```java
  // Criteria - java, typeSafety, dynamic
  CriteriaBuilder builder = entityManager.getCriteriaBuilder();
  CriteriaQuery<Member> query = builder.createQuery(Member.class);

  Root<Member> root = query.from(Member.class);

  CriteriaQuery<Member> criteriaQuery = query
        .select(root)
        .where(builder.equal(root.get("student_number"), "0991022"));
  Member member = entityManager.createQuery(criteriaQuery).getMember();
  ```

- **QueryDSL**
  - JPQL builder simpler

  ```java
  // QueryDSL - java, typeSafety, dynamic, easy
  JPAQuery query = new JPAQuery(entityManager);
  List<Customer> customers = query
        .from(customer)
        .where(customer.firstName.eq("bbbb"), customer.lastName.eq("cccc"))
        .list(customer);
  ```
