# Non-blocking Secondary Reads
```
https://www.mongodb.com/docs/manual/core/long-running-secondary-reads/
https://medium.com/geekculture/mongodb-read-from-secondary-to-boost-performance-dca938a680ac
```

mongo 4.x 부터 primary --(replication)-- secondary 동안 read 는 non-blocking 입니다. 
 
- primary 의 oplog collection 을 secondary 는 주기적으로 가져옴
  - 그 기간동안 (데이터 무결성을 위해) read 도 blocking 됨
- 그래서 주기적으로 높은 `Global Lock` 이 발생하여 read 성능이 저하됨
- MongoDB 4.0 이상부터 기존 버전의 스냅샷 기반으로 조회를 처리해서 이슈를 해결
  - 즉 MVCC 한다는 의미임