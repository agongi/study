# Clustered Index

```
@author: suktae.choi
- https://wslog.dev/mysql-index#4c8551fdf047448290cb393ad7cd51c6
```

<img src="1.png" width="50%">

Clustered Index 는 실제 ROWID (데이터의 주소) 를 가지고, !clustered-index (==secondary index) 는 PK 를 가진다.

CUD 발생시 RID(rowid) 는 데이터가 정렬되어야 하기 때문에 `페이지 분할`이 발생 할 수 있어, 인덱스 전체의 갱신이 발생합니다.
하지만 secondary-index 에서 ROWID 가 아닌 P.K 를 참조하면 -> 해당 인덱스 갱신은 clustered-index 에만 발생합니다.

> secondary-index 는 데이터의 정렬이 불필요하므로

즉 select 이득보다, craete/insert/delete 시 잃는 성능이 더 크므로 secondary-index 는 leaf node 에 p.k 를 참조합니다.