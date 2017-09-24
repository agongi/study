## JPA

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.09.23
ㅁ References:
```

### Index
- [Object-Oriented Query Language](object-oriented-query-language)

### JPA
TBD

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
