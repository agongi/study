## Domain-Driven Development

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.slideshare.net/madvirus/ddd-final
```

#### Blog

- [Effective Aggregate: Part I](https://dddcommunity.org/wp-content/uploads/files/pdf_articles/Vernon_2011_1.pdf)
- [Effective Aggregate: Part II](https://dddcommunity.org/wp-content/uploads/files/pdf_articles/Vernon_2011_2.pdf)
- [Effective Aggregate: Part III](https://dddcommunity.org/wp-content/uploads/files/pdf_articles/Vernon_2011_3.pdf)
- [@DomainEvents](https://www.baeldung.com/spring-data-ddd)

***

## 구성요소

- Domain
  - Entity
- Value
  - [Embeddable](https://github.com/cheese10yun/spring-jpa-best-practices/blob/master/doc/step-04.md)
- Aggregate
  - bounded context 의 단위 (ex. Value)
    - 각각의 aggregate 는 동일 Domain 에 속함 (bounded context)
    - Domain 에서의 Value 를 담당한다.
  - Business Command 를 받으면 특정 action 을 수행한다.
- Aggregate Root
  - Aggregate 의 진입점 (ex. Entity)
- (Domain) Service
  - 다른 Aggregate 끼리의 연동 (a.k.a. Facade)
- Repository

## Spring Data + DDD

### Relation (== Entity)

```java
@Entity
@Table
public class User {
  @Id
  @Column
  private Long userId;

  @OneToMany
  @JoinColumn(name = "userId")
  private Set<Order> orders;
}

@Entity
@Table
public class Order {
  @Id
  @Column
  private Long sequence;
  
  @Column
  private Long userId;
}
```

### Value (== Embeddable)

```java
@Entity
@Table
public class User {
  @Id
  @Column
  private Long userId;

  @Embedded
  private AddressInfo address;
}

@Embeddable
public class AddressInfo {
  // ..
}
```

