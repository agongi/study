## JPAQuery vs SQLQuery vs JPASQLQuery

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.slideshare.net/topcredu/jpajpa-querydsl-jpaquery-jpaquery-jpasqlquery-sqlquery-sqlqueryfactory
```

## JPAQuery

- Factory: JPAQueryFactory

EntityManager 를 사용해서, JPQL 쿼리로 질의합니다.

## SQLQuery

- Factory: SQLQueryFactory

JDBC 를 사용해서, SQL 쿼리로 질의합니다.

## JPASQLQuery

- Factory: x

EntityManager 를 사용해서, SQL 쿼리로 질의합니다.

