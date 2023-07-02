# Persistence Context

```
@author: suktae.choi
```

### Blog

- [Open_Session_In_View_Pattern.pdf](http://pds19.egloos.com/pds/201106/28/18/Open_Session_In_View_Pattern.pdf)

***

| Hibernate       | JPA                 |
|-----------------|---------------------|
| Session         | Entity Manager      |
| Session Context | Persistence Context |

## [Scope](https://colevelup.tistory.com/21)

entityManager 는 기본적으로 transaction-scope 로 동작합니다. 즉 현재 tx 를 실행하는 thread 단위에서만 영속성은 유효합니다

> 영속성은 동시성 이슈가 있으므로 thread 단위로 유니크해야 합니다

영속성을 관리하는 em 은 아래의 방법으로 가져옵니다:

- @PersistenceContext EntityManager em;
  - javax 에서 지원하는 방식입니다
  - new or tx 에서 사용중인 em 을 리턴합니다
- @Autowired EntityManager em;
  - 스프링빈은 기본적으로 싱글톤 이기 때문에 동시성 이슈가 있지만 (영속성이 다같이 공유됨)
  - proxy 가 반환되고 (runtime-weaving) 사용시점에 new or tx 에서 사용중인 em 으로 처리합니다
- EntityManager em = emf.createEntityManager();
  - 항상 instance 가 생성되지만 `다른 em 이라도 tx-scope 를 보고 같은 영속성 or 다른 영속성`을 바라볼지 결정되어 리턴됩니다

어노테이션을 주입 or 팩토리를 통한 생성 모두 `동일 트랜잭션 범위에서는` em instance 는 달라도 같은 영속성을 사용합니다.

<img src="3.png" width="75%">

트랜잭션이 다르면 동일한 엔티티 매니저를 사용해도 다른 영속성 컨텍스트를 사용합니다

```java
// hibernate 설정
props.put(org.hibernate.cfg.Environment.CURRENT_SESSION_CONTEXT_CLASS, "thread");

// SessionFactoryImpl - threadLocal 에 세션 저장
else if ( "thread".equals( impl ) ) {
  return new ThreadLocalSessionContext( this );
}
```

## OSIV

Session (== Entity Manager) 을 view 까지 확장해서 lazy-load (즉 N+1) 을 지원하는 개념입니다.

### 과거 OSIV

- 트랜잭션 범위
  - [FROM] filter/interceptor [TO] view
- 영속성 범위
  - [FROM] filter/interceptor [TO] view

<img src="1.png" width="75%">

### 스프링 OSIV

- 트랜잭션 범위
  - [FROM/TO] @Transactional
- 영속성 범위
  - [FROM] filter/interceptor [TO] view

<img src="2.png" width="75%">

트랜잭션은 종료되었지만 영속성만 존재할때 조회가 가능한 이유는 `tx 없는 select 가 가능하기 때문 (select for share 가 아닌 이상 모든 조회는 non-transactional read)` 입니다

view 에서 영속성에 대한 변경이 있어도 아래의 조건에 의해 DB 에 반영되지 않습니다

- 묵시적
  - 스프링 OSIV filter/interceptor 는 요청이 끝나면 em.close() 로 종료하므로 반영되지 않습니다
- 명시적
  - em.flush 을 명시적으롷 호출해도 tx 가 이미 종료된 상태이므로 TransactionRequiredException 예외가 발생합니다

