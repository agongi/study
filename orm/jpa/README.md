## JPA

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.09.23
ㅁ References:
```

### Index
- [Object-Oriented Query Language](object-oriented-query-language)
- [FetchType.LAZY vs EAGER](lazy-eager)
- [QueryDSL](http://www.querydsl.com/static/querydsl/4.0.0/reference/ko-KR/html_single/)
- [Spring Data JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/)

### Basic

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
