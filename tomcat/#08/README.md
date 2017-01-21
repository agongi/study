## 톰캣 #08 웹서버 연동
#### 개념

Tomcat으로만 간단한 웹서비스는 operate할수있는데, 왜 web(apache)와 연동을 해야 하는걸까?

이번 파트에서는, 다음 질문에 대한 답을 내릴수 있길 바란다.

여러가지 목적이 있겠지만, 크게 다음 2가지 benefit이 있어, web서버와 연동을 한다.

 - ~~Load Balancing (failover)~~
 - Static / Dynamic request 분리처리
 - was 직접접근 방지

apache의 설정으로 load balancing이 가능하지만, 대부분 L4(ELB)를 사용하므로, 논외로 하자. 가장 큰 이유는 [HTML, CSS, JS]로 표현되는 static file은 apache를 통해 처리하고, [JSP, Servlet]으로 표현할수 있는 dynamic은 tomcat을 통해 처리하는게 좋다.

그렇다면 왜 web을 통해 request를 받으면 좋을까?
 - static file의 response (응답속도)는 대체로 apache > tomcat 이다.
 - 모든 request는 web을 통해 전달된다.

```
web(public IP) --> was1(private IP)
               --> was2(private IP)
```

공격자가 해킹을 시도한다고 가정해보자. 외부에 오픈된 Server는 WEB뿐이므로, 공격으로부터 우리의 biz-logic을 수행하는 was는 안전하다.(비록 web서버가 down된다고 하더라도.. L4를 통해 충분히 다른 web으로 대체 가능하다.)

그리고 security/load balancing/cache등의 설정은 모두 web에 설정하고, was는 순수하게 biz-logic만 담당하는 구조로 진행하는것이, 추후 유지/보수에도 용이하다.

![img-web-was](https://github.com/agongi/study/blob/master/tomcat/%2308/images/Screen%20Shot%202015-07-15%20at%201.04.45%20AM.png)


#### mod_jk 설정 (web-was 연동)

- mod_jk 설정

apache에 mod_jk module을 load한다.

![img-mod_jk](https://github.com/agongi/study/blob/master/tomcat/%2308/images/Screen%20Shot%202015-07-14%20at%2012.50.31%20AM.png)

- worker 정의

HTTP request를 받을, web을 설정하고, 후단에 있는 was를 worker를 통해 설정한다.

![img-worker](https://github.com/agongi/study/blob/master/tomcat/%2308/images/Screen%20Shot%202015-07-15%20at%201.44.26%20AM.png)

- URL Pattern 정의

was로 bypass할 URL Pattern을 정의한다.

![img-url](https://github.com/agongi/study/blob/master/tomcat/%2308/images/Screen%20Shot%202015-07-15%20at%201.19.18%20AM.png)


#### cluster 설정 (failover)

web에서 단순, load balance만 진행한다고 가정하자.

```
web --> was1 (X)
    --> was2 (O)
```

was1에 연결된 사용자의 session은 was1 장애 발생 한 순간, 사라진다. was2는 was1가 사용한 세션에 대해 알지 못하기 때문이다.

해당 상황을 극복하기 위한 개념이 cluster이다.
>######모든 was가 session정보를 공유 (N-N)

사용을 위해선 server.xml을 설정해야한다.
 - <Membership>: 멤버 그룹정의. 동일 클러스터로 묶고자 하는 Tomcat 인스턴스들은 동일한 값으로 설정합니다.
 - <Receiver>: 클러스터 그룹으로부터의 메세지와 세션 정보를 받는 것으로, 각 인스턴스는 서로 다른 포트를 사용합니다.
 - <Sender>: 클러스터 그룹으로 자신의 정보를 전송합니다.

![img-cluster](https://github.com/agongi/study/blob/master/tomcat/%2308/images/Screen%20Shot%202015-07-14%20at%201.02.31%20AM.png)

하지만, 실제 PRD환경에서 tomcat에서 제공하는 cluster을 사용하는것은 무리가 있다. 별도 MemDB(redis, memcached) / infinispan 등을 이용해서, 분산캐시시 topology를 설정하는것이 더 현명하다.
