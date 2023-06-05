# JPA

```
@author: suktae.choi
- http://arahansa.github.io/docs_spring/jpa.html
- https://en.wikibooks.org/wiki/Java_Persistence/Relationships#Common_Problems
- https://docs.jboss.org/hibernate/orm/5.1/userguide/html_single/Hibernate_User_Guide.html
- https://zhangyuhui.blog/2018/01/29/jpa-transaction-hibernate-session-jdbc-connection-and-db-transaction/
```

### Index

- [FetchType.LAZY vs EAGER](lazy-eager)
- [EnumCodeConverter](enum-code-converter)
- [OSIV](osiv)
- [Session](session)
- [JPA Best Practices](https://github.com/cheese10yun/spring-jpa-best-practices)

### Blog

- [JPA에서 대량의 데이터를 삭제할때 주의해야할 점](https://jojoldu.tistory.com/235)
- [JPA N+1 문제 및 해결방안](https://jojoldu.tistory.com/165)
- [순환참조를 해결하는 방법](http://binarycube.tistory.com/1)
- [JPA 프로그래밍 정리](https://github.com/cheese10yun/TIL/blob/master/Spring/jpa/jpa.md)

***

## Persistence Context

entityManager 에서 관리되는 객체를 의미합니다. 영속상태는 아래의 조건을 만족하면 됩니다:

- (신규) new Object(); 를 통해 생성된 자바 객체를 \#save
- (조회) \#find 를 통해 조회한 entity

> 영속성은 tx 단위마다 생성됩니다 (정확히는 hibernate session 단위)

EntityManager 는 thread-safe 하지 않으므로, 공유하면 안되고 @PersistenceContext 를 통해 주입해야 합니다

- 일반적인 bean 으로 주입하면 안됩니다
  - thread 단위로 생성해야 하므로, singleton 이 기본인 bean 으로 사용 불가능
- @PersistenceContext 를 통해 주입받으면
  - EntityManagerFactory#createEntityManager 을 통해 생성하거나
  - 진행중인 transaction 이 있다면 -> tx 에서 사용중인 em 획득

## 기본키 매핑

- IDENTITY: auto increment 등 처럼 DB 에 위임
- SEQUENCE: 생성할 시퀀스를 지정 (generator)
- TABLE: 키 생성 전용 테이블 사용
- AUTO: dialect 에 따라 hibernate 에서 3가지 방식중 선택

## 컬럼 매핑

- @Column: 모든 컬럼에 정의 (생략시 묵시적으로 적용되지만, 명시하는게 나음)
- @Enumerated: ENUM 지정
- @Temporal: date, time, datetime 지정 (기본값은 datetime 이 모두 표현되는 timestamp)
- @Lob: clob (longtext), blob (나머지)
- @Transient: JPA 에서 관리하지 않을 field
- @Access
  - field 직접 접근 (private 이라도 접근)
  - getter/setter 통한 접근

## 연관관계 매핑

### @OneToMany/@ManyToOne

- @OneToMany(mappedBy = B)
  - 연관관계 대상
  - mappedBy 으로 연관관계 주인 필드명 지정
- @ManyToOne; @JoinColumn
  - 연관관계 주인 (F.K 을 정의한 쪽이 주인 입니다)
  - @JoinColumn 으로 F.K 지정

```java
/**
 * User Entity
 */
@OneToMany(fetch = FetchType.LAZY, mappedBy = "user")
private Set<Order> orders = new LinkedHashSet<>();

/**
 * Order Entity
 */
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "USER_ID")
private User user;
```

### @OneToOne

- 주 테이블에 F.K 정의
  - proxy 를 통한 lazy-load 가 가능합니다 (F.K is not null 이면 대상이 존재함이 보장되므로 proxy 사용가능 즉 eager 불필요)
    - proxy 가 아직 row 를 조회하진 않았지만 존재유무는 알아야 하므로 (proxy 와 null 은 다르다) 존재유무에 대한 보장이 필요
- 대상 테이블에 F.K 정의
  - eager-load 만 가능합니다

```java
/**
 * User Entity
 */
@OneToOne(mappedBy = "user")
private Order order = new LinkedHashSet<>();

/**
 * Order Entity
 */
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "USER_ID")
private User user;
```

### @ManyToMany

- @JoinTable
  - 연관관계 주인이 @JoinTable 을 명시합니다 (@JoinColumn 과 동일함)
  - 연관관계 대상은 mappedBy 를 명시합니다

```java
@ManyToMany(fetch = FetchType.EAGER)
@JoinTable(name = "TABLE_NAME", joinColumns = @JoinColumn(name = "PERSON_ID"), inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID"))
@Where(clause = "DEL_YN <> 1")   // 유효한 상품만 조회
@Fetch(FetchMode.SUBSELECT)
private Set<Order> orders = new LinkedHashSet<>();

@ManyToMany(mappedBy = "orders")
private Set<User> users = new LinkedHashSet<>();
```

@JoinTable 방식은 `joinColumns/inverseJoinColumns` 을 통해 2개의 컬럼만 사용가능해서 테이블 확장이 불가능합니다.

그래서 별도의 매핑테이블을 만들고 (대상1) OneToMany -- ManyToOne (매핑테이블) ManyToOne -- OneToMany (대상2) 로 연결하는게 확장성이 있습니다

<img src="1.png" width="75%">

// TODO 복합키 vs 기본키 + F.K

## 상속관계 매핑

- @Inheritance

N 개의 타입으로 상속관계매핑이 필요한 경우

```java

@Entity(name = "PersonInfo")
@Table(name = "PERS_INFO")
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorFormula("case when LOCT_TP in ('A','B) then 'KR' when LOCT_TP in ('C') then 'JP' else 'US' end")
public abstract class PersonInfo {
    @Column("name)
        private String name;

        @Column("LOCT_TP")
        @DiscriminatorColumn("type")
        private LocationType locationType;
}


```

컬럼당 1개의 상속관계가 정의된다면

```java

@DiscriminatorValue("KR")
public class KrPersonInfo extends PersonInfo {
    // ..
}

@Entity(name = "PersonInfo")
@Table(name = "PERS_INFO")
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
@DiscriminatorColumn(name = "LOCT_TP")
public abstract class PersonInfo {
    @Column("name)
        private String name;

        @Column("LOCT_TP")
        private LocationType locationType;
}

@DiscriminatorValue("KR")
public class KrPersonInfo extends PersonInfo {
    // ..
}
```

- @MappedClass - 컬럼에 영향을 끼치지 않음 (단순 코드상 상속)

## 복합키 매핑

복합키를 P.K 로 사용하기보다, P.K 는 따로 사용하고 복합키로 정의 필요한 F.Ks 를 U.K 로 잡는게 사용성이 더 좋습니다

> 조회할때 마다 복합키를 만들어야 하는 단점 상쇄

- @IdClass
- @EmbeddedId - @Embeddable

## 조인테이블 매핑

매핑테이블을 별도로 지정하는 방식이다

- @JoinTable





