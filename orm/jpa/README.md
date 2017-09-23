## JPA

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.09.23
ㅁ References:
- http://yellowh.tistory.com/
- http://www.mkyong.com/tutorials/hibernate-tutorials/
- https://www.youtube.com/playlist?list=PLvudjKUrAA6YNHxI1xiLcGtBhuXPwNAxk
- http://headcha.tistory.com/1
```

### Temp
Spring + JPA
```java
@Repository
public class UserRepository {
  @autowired
  private EntityManager entityManager;

  public void insert(User user) {
    entityManager.persist(user);
  }
}
```

Spring-Data-JPA
```java
// Spring will generate Impl class
public interface UserRepository extends JpaRepository<User, Long> {
  // nothing ..
}
```

```java
// JPQL - text, non-typeSafety
Query query = entityManager.createQuery("SELECT o FROM Order o");

// JPQL - text, non-typeSafety, static
TypedQuery<Order> typedQuery = entityManager.createQuery("SELECT o FROM Order o", Order.class);

// Criteria - java, typeSafety, dynamic
CriteriaBuilder builder = entityManager.getCriteriaBuilder();
CriteriaQuery<Employee> query = builder.createQuery(Employee.class);
Root<Employee> employee = query.from(Employee.class);
query.where(builder.and(
    builder.gt(employee.get(Employee_.salary), 5000),
    builder.le(employee.get(Employee_.salary), 9000)
));
List<Customer> customers = entityManager.createQuery(query.select(employee)).getResultList();

// QueryDSL - java, typeSafety, dynamic, easy
JPAQuery query = new JPAQuery(entityManager);
List<Customer> customers = query.from(customer).where(customer.firstName.eq("bbbb"), customer.lastName.eq("cccc")).list(customer);
```

### Persistence
#### Spring Data JPA

#### JPA


#### Hibernate
Implementation of JPA, provides the under the hood functionality

#### JPQL (JPA Query Language)
> 고려할점

- 묵시적 JOIN
  - @OneToMapy : 기본값=지연로딩(LAZY)
  - @ManyToOne : 기본값=즉시로딩(EAGER)=> Order 정보 이외에 N : 1 관계에 있는 Entity를 묵시적으로 로딩해 와서 지연이 된 사례

> 쉽게 사용하기위한 Builder Patterns

- Criteria
  - JPQL builder complexed
- **QueryDSL**
  - JPQL builder simpler


우선 Jpa 를 사용하면서 객체간 관계로 Join 하는 것은 가능하지만 where 절을 사용하는 것은 제한 된다.
예를 들어 parent repository 가 존재한다고 가정하자.
findByName ..과 같이 parentName 으로 조회하는 것은 jpa 가 지원 해 주지만 parent , child 관계 에서 parent 와 child 를 join 해 조건을 주는 것은 불가능 하다.  ( ex : select * from parent , child where parent.name = aaa and child.name=aaa )
이런 문제를 해결 해 주는 것이 바로 JPQL ( java persistence Query Language ) 이다
JPA 가 일부 JPQL 을 사용해 where 조건을 넣는 것은 추상화 시켰지만 join 등 복잡한 상황에 대해서는 구현이 제한 되어 있기 때문에 JPQL 로 해결 해야 한다.
위 언급한 Criteria 는 Jpa2.0 부터 지원한 JPQL 빌더에 불과 하다. 즉 JPQL 를 사용했을때 위 언급한 쿼리가 장황해 가독성이 떨어지는 문제를 해결 하고자 만들어 진 라이브러리 이지만 여전히 사용이 복잡하다.
QueryDsl 은 Criteria 를 사용해도 코드가 장황해지는 문제를 해결 하기 위해 나온 open source 기반 JPQL 빌더이다.
정리 하자면 Jpa 가 지원해주는 query 기능은 제한 적이며 이를 해결 하기 위해서는 java 표준 쿼리인 JPQL 을 사용해야 한다.
JPQL 은 문법이 복잡해 이를 지원해주는 criteria 혹은 QueryDsl 이 있다. 이다.
