# OSIV

```
@author: suktae.choi
- https://vladmihalcea.com/the-open-session-in-view-anti-pattern/
```

### Blog

- [OSIV](http://pds19.egloos.com/pds/201106/28/18/Open_Session_In_View_Pattern.pdf)

***

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
    ​    if (TransactionSynchronizationManager.hasResource(sessionFactory)) {
    ​       participate = true;
    ​    }
    ​    else {
    ​       boolean isFirstRequest = !isAsyncDispatch(request);
    ​       if (isFirstRequest || !applySessionBindingInterceptor(asyncManager, key)) {
    ​          // open session
    ​          logger.debug("Opening Hibernate Session in OpenSessionInViewFilter");
    ​          Session session = openSession(sessionFactory);
    ​          SessionHolder sessionHolder = new SessionHolder(session);
    ​          TransactionSynchronizationManager.bindResource(sessionFactory, sessionHolder);

    ​          AsyncRequestInterceptor interceptor = new AsyncRequestInterceptor(sessionFactory, sessionHolder);
    ​          asyncManager.registerCallableInterceptor(key, interceptor);
    ​          asyncManager.registerDeferredResultInterceptor(key, interceptor);
    ​       }
    ​    }

    ​    try {
    ​       filterChain.doFilter(request, response);
    ​    }

    ​    finally {
    ​       if (!participate) {
    ​          // close session
    ​          SessionHolder sessionHolder =
    ​                (SessionHolder) TransactionSynchronizationManager.unbindResource(sessionFactory);
    ​          if (!isAsyncStarted(request)) {
    ​             logger.debug("Closing Hibernate Session in OpenSessionInViewFilter");
    ​             SessionFactoryUtils.closeSession(sessionHolder.getSession());
    ​          }
    ​       }
    ​    }
    }
```