## Execution Plan

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.25
ㅁ References:
 - http://devse.tistory.com/entry/MYSQL-%EC%8B%A4%ED%96%89%EA%B3%84%ED%9A%8D-%EB%B6%84%EC%84%9D-%EB%B0%A9%EB%B2%95-%EC%9A%94%EC%95%BD
 - http://asuraiv.blogspot.kr/2015/08/mysql-query-plan.html
```

### Usage
```sql
EXPLAIN SELECT * FROM categories
```
```sql
********************** 1. row **********************
           id: 1
  select_type: SIMPLE
        table: categories
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 4
        Extra:
1 row in set (0.00 sec)
```

- type
  - index - index full scan
  - all - table full scan

<img src="https://github.com/agongi/study/blob/master/rdbms/execution-plan/images/MySQL-%EC%8B%A4%ED%96%89%EA%B3%84%ED%9A%8D%20%EB%B6%84%EC%84%9D%20%EB%B0%A9%EB%B2%95.png">
