## JPA

```
ㅁ Author: suktae.choi
ㅁ References:
- http://arahansa.github.io/docs_spring/jpa.html
- https://en.wikibooks.org/wiki/Java_Persistence/Relationships#Common_Problems
```

### Index
- [Object-Oriented Query Language](object-oriented-query-language)
- [FetchType.LAZY vs EAGER](lazy-eager)
- [EnumCodeConverter](enum-code-converter)

#### Blog
- [JPA에서 대량의 데이터를 삭제할때 주의해야할 점](https://jojoldu.tistory.com/235)
- [JPA N+1 문제 및 해결방안](https://jojoldu.tistory.com/165)
- [Spring - Open Session In View](http://kingbbode.tistory.com/27)
- [순환참조를 해결하는 방법](http://binarycube.tistory.com/1)
- [OSIV](http://pds19.egloos.com/pds/201106/28/18/Open_Session_In_View_Pattern.pdf)

@Transactional 이 begin; JDBC 연결이 수행됨
hibernate session 을 연다; 영속성이 생성됨 (관리됨)
영속성은 tx 단위마다 생성됨 -> (좀더 정확히는 hibernate session 단위로 생성됨)

dirty check - persist 상태인 객체의 상태변화를 감지 (코드상에서 값 바꾸는거)
flush - db 에 반영한다
proxy init - lazy-load 로 프록시만 들고있는데, 실제 property 에 접근해서 객체를 가져오는것

service 에서 tx 가 닫혔고 (detached 됨, lazy-loading 인 관계는 proxy 만 hold 하고 있는 상태)
뷰 렌더딩 시점에 proxy 만 가지고있는 연관 entity 에 접근하면 에러발생 (프록시 초기화 (proxy-load) 는 persist 상태일때만 가능하다)
: fetch-join 으로 모든 entity 를 load 한 후, tx 종료(세션닫힘, detached 로 됨. 하지만 이미 내용은 가지고있음)
: OSIV

트랜잭션이 닫힌후 (session 은 남아있음, JDBC 는 반환됨) 아직 persist 상태인 엔티디의 lazy-load (프록시 초기화) 가 가능한 이유는
"트랜잭션 미적용 데이터 접근" 을 hibernate 에서 허용해서이다. (묵시적으로 JDBC 연결, tx 생성, 처리, tx 종료, JDBC 반환이 일어남)
> auto commit mode; 로 데이터에 접근하는 의미 (console 에서 우리가 쿼리 날리는것처럼)
