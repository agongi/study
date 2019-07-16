## Reader

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/336?category=635883
- http://www.mybatis.org/spring/batch.html
```

### Cursor

stream fetchSize 단위로 계속 읽음
1개의 connection 이 유지되면서 스트리밍으로 데이터 처리

#### JdbcCursorItemReader

#### HibernateCursorItemReader

#### StoredProcedureItemReader

#### MyBatisCursorItemReader



### Paging

limit (pageSize)
offset (pageNo)
각각의 쿼리수행시 connection 연결/끊음이 반복

select *
from nc_xxx
limit {no}, {size}
order by id;

> 페이징시 정렬을 하지않으면, 누락/중복 발생가능성있음

#### JdbcPagingItemReader

#### HibernatePagingItemReader

#### JpaPagingItemReader

#### MyBatisPagingItemReader



### List

#### ListItemReader