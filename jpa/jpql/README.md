# JPQL

```
@author: suktae.choi
- https://www.nowwatersblog.com/jpa/ch10
```

## 기본적인 사용법

```java
// ANSI SQL
em.createNativeQuery("SELECT * FROM MEMBER WHERE ID = '243'",Member.class)
    .getResultList();

// JPQL (or HQL)
em.createQuery("SELECT m FROM Member m WHERE m.id = :id",Member.class)
    .setParameter("id","243")
    .getSingleResult(); // 결과가 없거나 1개 이상이면 exception

// Named - @NamedQuery 로 선언된 JPQL 을 사용
@Entity
@NamedQuery(
    name = "Member.findById",
    query = "SELECT m FROM Member m where m.id = :id")
public class Member {
    // ..
}

List<Member> resultList = em.createNamedQuery("Member.findById", Member.class)
    .setParameter("id", "243")
    .getSingleResult();
```

SQL 을 직접 사용하더라도 em 을 사용하므로 영속성에서 관리됩니다. (projection 하지않고 Entity.class 를 직접 전달한 경우)

> JPQL 을 사용해도 결국엔 SQL 이 DB 에서 실행되므로 표현의 방식일 뿐입니다

## TypeQuery vs Query

- TypeQuery
  - 반환 타입을 지정한 경우
- Query
  - 반환 타입이 없는 경우
  - SELECT 절의 조회 대상이 하나면 Object, 여러개면 Object[]

## Projection

- 엔티티 (@Entity)
  - @Id 가 있으므로 영속성에 관리됩니다
- 엠베디드 (@Embeddable) 
- 스칼라 (숫자, 문자 등 기본 데이터)
  - (단순한 값이므로) 영속성에 관리되지 않습니다

## Join

- inner
  - join 으로만 명시했을때의 기본 동작입니다
- left
- theta
  - where clause 에 조인조건을 명시하는 방식입니다
  - ```select m from Member m, Team t where m.username = t.name```
  - where 조건을 사용하므로 inner join 과 결과가 동일합니다
- on
  - JPQL 문법에서 join 구문에 on 은 불필요합니다 (Entity 의 관계를 통해 알아서 분석)
  - 대신 조인 대상의 필터링을 위한 on 은 지원합니다
  - ```select m, t from Member m left join m.team t on t.name = 'A'```
- fetch
  - ```select m from Member m left join fetch m.team```
  - fetch 라는 문법을 사용하면 eager-load 합니다
  - hibernate 는 collection fetch join 후 페이징 시 (limit) warn logging 을 남기며 메모리에서 페이징 처리합니다

## Distinct

left join (1-N 관계) 은 조회결과에 중복이 가능합니다. (driven-entity 개수만큼 N 건의 driving-entity 가 반환되므로)

- JPQL 에서 DISTINCT 사용
- SQL 에 DISTINCT 추가
  - 하지만 SQL 은 조회한 각 로우의 데이터가 다르므로 SQL DISTINCT 는 효과가 없습니다
- `애플리케이션에서 한번 더 중복 제거`

## SubQuery