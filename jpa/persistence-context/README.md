# Persistence Context
```
https://www.baeldung.com/jpa-hibernate-persistence-context
```

### Blog
- [Open_Session_In_View_Pattern.pdf](Open_Session_In_View_Pattern.pdf)
***
| Hibernate       | JPA                 |
|-----------------|---------------------|
| Session         | Entity Manager      |
| Session Context | Persistence Context |

## 특징
- DB 와 애플리케이션 사이의 1차 캐시 역할을 수행합니다
  - repeatable read 수준의 격리 보장 (1차캐시를 통해 조회하므로 기존 값이 조회됨)
- 영속성에 저장되는 Entity 는 @Id (동등성 식별위한) 가 필수

### 변경감지
JPA는 엔티티를 영속성 컨텍스트에 보관할 때, 최초 상태 `스냅샷` 을 저장하고 플러시 시점에 스냅샷과 비교해서 변경된 엔티티를 감지 합니다:
- 현재 엔티티와 스냅샷을 비교해서 변경된 엔티티를 찾은후
- Dirty Check 된 엔티티의 Update SQL 를 생성합니다
  - 기본적으로 Update SQL 은 모든 필드를 업데이트 하는 동일 SQL 을 사용합니다 (쿼리 재사용하여 DB 성능 향상)
  - 변경된 필드만 포함하는 SQL 을 동적으로 생성하려면 `@DynamicUpdate/@DynamicInsert` 을 Entity 에 선언
- Flush (DB 에 SQL 전달해서 반영) & Commit

### Flush
- em#flush 직접 호출
- 트랜잭션 커밋 시 자동 호출
- `JPQL 쿼리 실행 시` 자동 호출
  - JPQL 쿼리를 생성하는 `QueryDSL 사용`해도 자동 호출 됩니다
  - JPQL 은 DB 를 직접 조회하므로 현재 영속성의 값과 다른 데이터를 조회 할 수 있습니다 (영속성의 1차캐시로 인한 쓰기지연)
  - 그래서 `현재까지의 영속성 내용이 JPQL DB 직접 조회에 반영 하기 위해` 쿼리 수행전 flush 를 수행합니다 (flushAutomatically=true)
  - 만약 JPQL 로 DB 를 직접 수정하는 내용이 있다면 -> 영속성에는 해당 내용이 반영 전 입니다. 그래서 clearAutomatically=true 를 설정해서 이후 영속성에 재조회해서 반영 되도록 선언 합니다
  
```java
@Modifying(clearAutomatically = true, flushAutomatically = true)
public void updateUser(String id);
```

### [Scope](https://colevelup.tistory.com/21)
EntityManager 는 현재의 DB 커넥션에 유효합니다. 즉 현재 실행되고 있는 Transaction 단위에서 영속성은 유지 됩니다

> 동시성 이슈가 있으므로 Thread 간에 공유하거나 재사용 하면 안됨

<img src="3.png" width="50%">

- EntityManagerFactory 는 hibernate 설정을 읽어서 EntityManager 를 제공하는 역할
- emf.createEntityManager 를 통해 생성된 객체는 아직 커넥션 사용전
  - Transaction 이 시작되는 시점까지 (지연로딩) 커넥션 사용은 지연됩니다
  - ConnectionPool 애플리케이션이 설정 시점에 제공 합니다 (ex. hikari)
- 한번 생성된 EntityManager 는 절대 다른 스레드에 공유 하면 안됩니다

```java
// hibernate 설정
props.put(org.hibernate.cfg.Environment.CURRENT_SESSION_CONTEXT_CLASS,"thread");

// SessionFactoryImpl - threadLocal 에 세션 저장
else if("thread".equals(impl)){
  return new ThreadLocalSessionContext(this);
}
```

## OSIV
Session (== Entity Manager) 을 view 까지 확장해서 lazy-load (즉 N+1) 을 지원하는 개념입니다.

> 트랜잭션 종료시 커넥션 (DBCP) 을 반납하지 않고, view (최종 응답) 까지 유지 

### 스프링 OSIV
- 트랜잭션 범위
  - [FROM/TO] @Transactional
- 영속성 범위
  - [FROM] filter/interceptor [TO] view

<img src="2.png" width="50%">

트랜잭션은 종료되었지만 영속성만 존재할때 조회가 가능한 이유는 `tx 없는 select 가 가능하기 때문 (select for share 가 아닌 이상 모든 조회는 non-transactional read)` 입니다

view 에서 영속성에 대한 변경이 있어도 아래의 조건에 의해 DB 에 반영되지 않습니다

- 묵시적
  - 스프링 OSIV filter/interceptor 는 요청이 끝나면 em.close() 로 종료하므로 반영되지 않습니다
- 명시적
  - em.flush 을 호출해도 tx 가 이미 종료된 상태이므로 TransactionRequiredException 예외가 발생합니다

## READONLY
- 메모리 최적화 `(스냅샷 미저장)`
  - 읽기 전용 쿼리 힌트
  - 읽기 전용 엔티티 `@Immutable`
- 속도 최적화 `(스냅샷 미비교)`
  - 읽기 전용 트랜잭션

```java
@Transactional(readOnly = true) // 읽기 전용 트랜잭션
public Collection<DataEntity> findAll() {
    return em.createQuery("select d from DataEntity d", DataEntity.class)
        .setHint("org.hibernate.readOnly",true) // 읽기 전용 쿼리 힌트
        .getResultList();
}
```
