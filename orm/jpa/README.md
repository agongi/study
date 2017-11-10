## JPA

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.09.23
ㅁ References:
- http://wonwoo.ml/index.php/post/828
- http://yellowh.tistory.com/122
```

### Index
- [Object-Oriented Query Language](object-oriented-query-language)
- [FetchType.LAZY vs EAGER](lazy-eager)
- [QueryDSL](http://www.querydsl.com/static/querydsl/4.0.0/reference/ko-KR/html_single/)
  - [extra](https://doohwan-yoo.github.io/querydsl/)
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

<img src="images/Screen%20Shot%202017-10-14%20at%2018.37.01.png" width="75%">

### Draft - 용어정리
JPA는 Java Persistence API (표준)

Hibernate 는 JPA 의 구현체

Join 이나 복잡한 관계는 JPA 로 힘듬, 그래서 Query 를 작성해야 하는데..
- JPQL (Java Persistence Query Language) / HQL (Hibernate Query Language)
  - JPA 에서 제공하지만, 매우 복잡함
- Criteria
  - JPA 에서 제공하지만, 비슷하게 복잡함
- QueryDSL
  - 그나마 쓸만함
- @Query("select ... from ... where ...")
  - Spring-Data 에서 제공, repository interface 에 메소드 선언하고 @Query 어노테이션으로 쿼리 직접작성

Spring-Data-JPA 는 단순하게 반족되는 CRUD Repository 및 Query 작성을 자동으로 지원
