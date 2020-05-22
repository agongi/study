## QueryDSL

```
ㅁ Author: suktae.choi
ㅁ References:
- http://www.querydsl.com/static/querydsl/4.0.1/reference/ko-KR/html_single
- https://joont92.github.io/jpa/QueryDSL
- https://www.baeldung.com/intro-to-querydsl
```

#### Blog
- [JPASubQuery vs JPAExpressions](https://jojoldu.tistory.com/379?category=637935)
- [연관관계 없이 Join 조회하기](https://jojoldu.tistory.com/396)

***

<img src="images/Screen%20Shot%202017-10-14%20at%2018.37.01.png" width="75%">

## 사용방법

기본적으로 JPQL 을 정적 QClass 를 통해 작성한다고 보면된다.

쿼리는 JPAQuery or HibernateQuery 를 통해 생성한다. XYZQuery 를 만들기위한 빌더인 XYZQueryFactory 의 사용이 권장된다.

- JPQLQuery
  - JPAQuery
  - HibernateQuery
- QueryBuilder
  - JPAQueryFactory
  - HibernateQueryFactory

### R (Select)

```java
JPAQueryFactory query = new JPAQueryFactory(em);
QCustomer customer = QCustomer.customer;

Customer bob = query.from(customer)
  .where(customer.firstName.eq("Bob"))
  .uniqueResult(customer);
```

### CUD

```java
// update
queryFactory.update(user)
  .where(user.login.eq("Ash"))
  .set(user.login, "Ash2")
  .set(user.disabled, true)
  .execute();
```

```java
// delete
queryFactory.delete(user)
  .where(user.login.eq("David"))
  .execute();
```

Factory 를 이용하지 않은 직접사용은 아래와 같다:

### R (Select)

```java
JPAQuery query = new JPAQuery(em);
QCustomer customer = QCustomer.customer;

Customer bob = query.from(customer)
  .where(customer.firstName.eq("Bob"))
  .uniqueResult(customer);
```

### CUD

```java
// update
new JPAUpdateClause(em, QMember.member)
  .set(QMember.member.name, "newName")
  .where(QMember.member.id.eq(id));
```

```java
// delete
new JPADeleteClause(em, QMember.member)
  .where(QMember.member.id.eq(id));
```

## [Projections](https://icarus8050.tistory.com/5)

links - http://www.querydsl.com/static/querydsl/latest/reference/html/ch03s02.html

### QClass 가 있는 Entity

```java
List<Tuple> result = query
  .select(employee.firstName, employee.lastName)
  .from(employee).fetch();

for (Tuple row : result) {
     System.out.println("firstName " + row.get(employee.firstName));
     System.out.println("lastName " + row.get(employee.lastName));
}}
```

```java
List<Tuple> result = query
  .select(new QTuple(employee.firstName, employee.lastName))	// 동일한 결과이다
  .from(employee).fetch();

for (Tuple row : result) {
     System.out.println("firstName " + row.get(employee.firstName));
     System.out.println("lastName " + row.get(employee.lastName));
}}
```

Tuple 응답이 내려오므로, Entity 를 직접 생성하는 방법을 찾아보자

```java
@Entity
class Employee {

  @QueryProjection
  public Employee(long id, String name) {
    // ...
  }
}
```

```java
QEmployee employee = Employee.employee;
JPQLQuery query = new HibernateQuery(session);

List<Customer> dtos = query.select(QEmployee.create(employee.firstName, employee.lastName))
  .from(employee).fetch();
```

> 생성되는 create method 는 Projections.constructor 를 사용한다. 즉 직접사용도 가능

### QClass 가 없는 DTO

- 생성자

```java
List<UserDTO> dtos = query.select(
  Projections.constructor(UserDTO.class, user.firstName, user.lastName)).fetch();
```

아니면 생성자 생성인 경우에는 QClass 를 생성해서, compile-error 로 잘못된 타입지정을 막을 수 도 있다.

```java
class EmployeeDTO {

  @QueryProjection
  public EmployeeDTO(long id, String name) {
    // ...
  }
}
```

```java
QEmployee employee = Employee.employee;
JPQLQuery query = new HibernateQuery(session);

// Entity 와 생성법이 다르다. #create 를 사용하지않고 직접 생성
List<Customer> dtos = query.select(new QCustomerDTO(customer.id, customer.name))
  .from(employee).fetch();
```

- Field

Setter 를 통해 접근

```java
List<UserDTO> dtos = query.select(
  Projections.bean(UserDTO.class, user.firstName, user.lastName)).fetch();
```

Reflection 을 통해 직접접근

```java
List<UserDTO> dtos = query.select(
  Projections.fields(UserDTO.class, user.firstName, user.lastName)).fetch();
```

Expression\<?\>... 를 넘기는 방식이라 필드명 불일치/생성자 불일치 등의 에러는 Runtime 시점에만 확인 가능하다.

## 서브쿼리

https://jojoldu.tistory.com/379

JPASubQuery 대신 JPAExpressions

## Criteria 생성

BooleanExpression 이용해서 Criteria -> toArray expressions 패턴

## Relation 없는 조인

Hibernate 5.1 이상부터 가능

## CUD Bulk

1. spring-data 를 사용할 것인가?
   1. @Modifying(clearAutomatically = true) @Query("update ... ") 
   2. 끝
2. 아니요.
   1. 그렇다면 영속성이 필요한가?
      1. QueryFactory and/or JPAUpdateClause
   2. 아니요.
      1. JDBCTemplate 직접사용

## Dirty Check

영속성에서 최초조회의 스냅샷을 flush 시점에 확인하고, 변경이 있다면 entity (모든 필드) update 쿼리가 날아감

> 스냅샷 비교과정은 Objects#equals 로 비교하므로 무거운 작업이다.

### @DynamicUpdate

전체필드를 update query 에 실어서 보냄. payload 가 크므로 부하가큼

이런 경우변경 필드만 업데이트 되도록, 어노테이션을 타입에 선언하면됨

### @Transactional(ReadOnly)

readOnly tx 로 마킹되었다면, em.flush 가 발생하지않음

- em#flush 시 수행하는 dirty-check 생략
- 그에 따른 성능향상

## [동작원리 (영속성)](https://github.com/cheese10yun/blog-sample/blob/master/query-dsl/docs/jpa-persistence-context.md)

### EntityManager 직접사용 or Spring Data

- 영속성 컨텍스트 조회
  - 없으면, DB 조회
  - 영속성 컨텍스트에 저장
- 결과 리턴

### JPQL (즉 JQPL 을 생성하는 QueryDSL)

- DB 조회
- 영속성 컨텍스트에 저장
  - 이미 동일한 식별자 (@Id) 가 있다면 DB 조회 결과는 Discard
- 영속성 결과를 리턴

이런 의도치 않은 결과를 방지하기위해, #flush 자동호출 설정이 필요하다.

### Flush Mode

1. 명시적으로 em.flush() 호출
2. Transaction 종료시, flush() 자동호출
3. JPQL 실행시점에, flush() 자동호출
   1. 영속성에 동일한 @Id 의 entity 가 있음
   2. JPQL 쿼리 실행시점에, context#flush 자동호출
   3. 그후 DB 조회가 발생하면 원했던 결과가 나옴

## Appendix

### MySQL에선 Group by를 하면 정렬도 수행된다고? 

https://jojoldu.tistory.com/477

### batch_size

https://jojoldu.tistory.com/457



