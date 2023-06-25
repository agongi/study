# Persistence Context

```
@author: suktae.choi
```

### Blog

- [OSIV](http://pds19.egloos.com/pds/201106/28/18/Open_Session_In_View_Pattern.pdf)

***

## 영속성

em.find 와 em.createQuery 의 차이

- find 는 영속성에 있으면 가져옴 없으면 쿼리실행
- createQuery 는 무조건 쿼리실행 그리고 영속성에 값이 있으면 버리고, 없으면 관리
  - @Id 가 매칭되는 엔티티가 이미 영속성에 관리되는 경우 SQL 의 조회결과가 버려집니다. (영속성에서의 변경사항을 유지하기위함)
  - 그래서 JPQL 실행후 영속성을 clear 해주는게 중요합니다 (그래야 다음 조회시 영속성에 없으므로 쿼리 실행되어 영속성에 적재되므로)
  - 그리고 JPQL 실행전 현재까지의 dirty 가 flush 되야합니다. (그래야 쿼리시 현재까지의 작업내용이 반영됨)

```
app -> JPQL (flush) -> DB
               |          
           ---------
          |  영속성  |
           ---------
               |
app <- JPQL (clear) <- DB
```

### 쿼리 실행의 의미

spring-data or querydsl (== JPQL) or em 을 통한 native query 모두 쿼리가 실행된다는 관점은 동일합니다.

em.find 만이 최초조회가 아닌 2번째 부터의 조회시 영속성에 엔티티가 존재하므로 쿼리가 실행되지 않습니다.

em.createQuery, createNativeQuery 가 최종적으로 실행되는 경우는 모드 쿼리 실행입니다.

대신 jdbcTemplate 을 통해 직접 sql 을 실행하는 경우는 JPA 에서 인지할 수 없으므로 flush 를 명시적으로 해줘야합니다.

### Flush

- AUTO
  - COMMIT 시
  - 쿼리 실행전
- COMMIT
  - COMMIT 시

## Session (== Entity Manager)

### Lifecycle

- Open (hibernate) session (in terms of JPA, create EntityManager)
- Open physical connection (JDBC)
- tx begin;
  - ... CRUD
- commit; or rollback;
- Close physical connection
- Close session

<img src="2.png" width="75%">

### Session Context

- entityManager 는 application-scope 으로 관리됨
- session 은 hibernate 설정에 따르 context 결정
  - thread 로 설정되어 있다면
- implicit tx 가 시작된다면
  - session opened
  - JDBC opened
  - tx begin; commit; or rollback;
  - JDBC closed
  - session closed
- 그다음 implicit tx 시작시
  - entityManager.getCurrentSession();
  - session 이 threadLocal 단위로 관리되므로, mark as closed
  - exception throws

```java
// hibernate 설정
props.put(org.hibernate.cfg.Environment.CURRENT_SESSION_CONTEXT_CLASS, "thread");

// SessionFactoryImpl - threadLocal 에 세션 저장
else if ( "thread".equals( impl ) ) {
  return new ThreadLocalSessionContext( this );
}

public User getUser(Long id) {
  User user = new User(id);
  user.setName(userService.getName(id));	// implicit session open and mark closed
  user.setAddress(userService.getAddress(id));	// session fetches from threadLocal and fail

  return user;
}

// console 결과
sessionAlreadyClosedException
```

## OSIV

service 에서 tx 가 닫혔고 (detached 됨, lazy-loading 인 관계는 proxy 만 hold 하고 있는 상태)
뷰 렌더딩 시점에 proxy 만 가지고있는 연관 entity 에 접근하면 에러발생 (프록시 초기화 (proxy-load) 는 persist 상태일때만 가능하다)
: fetch-join 으로 모든 entity 를 load 한 후, tx 종료(세션닫힘, detached 로 됨. 하지만 이미 내용은 가지고있음)

<img src="1.png" width="75%">

- OpenSessionInViewFilter (servlet-filter) 진입에서 hibernate#openSession
  - dispatcher-servlet -- controller -- service -- repository 까지 수행
  - controller 에서 return
- OpenSessionInViewFilter (servlet-filter) 에서 hibernate#closeSession

```java
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        if (TransactionSynchronizationManager.hasResource(sessionFactory)) {
            participate = true;
        } else {
            boolean isFirstRequest = !isAsyncDispatch(request);
            if (isFirstRequest || !applySessionBindingInterceptor(asyncManager, key)) {
                // open session
                logger.debug("Opening Hibernate Session in OpenSessionInViewFilter");
                Session session = openSession(sessionFactory);
                SessionHolder sessionHolder = new SessionHolder(session);
                TransactionSynchronizationManager.bindResource(sessionFactory, sessionHolder);

                AsyncRequestInterceptor interceptor = new AsyncRequestInterceptor(sessionFactory, sessionHolder);
                asyncManager.registerCallableInterceptor(key, interceptor);
                asyncManager.registerDeferredResultInterceptor(key, interceptor);
            }
        }

        try {
            filterChain.doFilter(request, response);
        } finally {
            if (!participate) {
                // close session
                SessionHolder sessionHolder =
                    (SessionHolder)TransactionSynchronizationManager.unbindResource(sessionFactory);
                if (!isAsyncStarted(request)) {
                    logger.debug("Closing Hibernate Session in OpenSessionInViewFilter");
                    SessionFactoryUtils.closeSession(sessionHolder.getSession());
                }
            }
        }
    }
```
