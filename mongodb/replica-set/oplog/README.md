# Oplog

```
@author: suktae.choi
- https://atharva-inamdar.medium.com/understanding-mongodb-oplog-249f3996f528
- https://hevodata.com/learn/working-with-mongodb-oplog/
```

The oplog (operations log) is `a special capped collection` that keeps a rolling record of all operations that modify the data stored in your databases.

- Primary 는 write 발생시 oplog 에 기록
- Secondary 는 주기적으로 oplog 를 가져와서 반영 (async)
  - 자신의 `local.oplog.rs` collection 에 복사본을 만듬
  - (default) size: 5% of disk/time: 24-hours

> node 가 투입되었을때, oplog 로그로 모든 변경을 따라갈 수 있다면 바로 init sync 과정을 피할수 있다.
