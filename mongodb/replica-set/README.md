# ReplicaSet

```
@author: suktae.choi
- https://docs.mongodb.com/manual/replication/
```

<img src="1.png" width="50%"/>

## Members
### Primary
- 모든 write 연산은 primary 에서만 수행

### Secondary
- oplog 를 받아서 데이터 복제 (replication)
- read 연산은 secondary 에서도 가능

### Arbiter
- replication 은 하지 않고 election 에만 참여

### Multiple Arbiter
arbiter 가 election 에 참여할때 secondary 는 본인의 latest offset 을 기준으로 next-primary 를 선정하는데 arbiter 는 그냥 무조건 먼저온 응답에 대해 OK 합니다.

따라서 뒤쳐진 secondary 가 primary 로 선정될수 있고 이는 rollback 을 야기합니다. 이를 방지하기 위해 arbiter 는 최대1개만 유지하는게 좋습니다.

## Oplog (Collection)
The oplog (operations log) is `a special capped collection` that keeps a rolling record of all operations that modify the data stored in your databases.

- Primary 는 write 발생시 oplog 에 기록
- Secondary 는 주기적으로 oplog 를 가져와서 반영한다. (async)
  - 자신의 `local.oplog.rs` collection 에 복사본을 만듬
  - (default) size: 5% of disk/time: 24-hours

> node 가 투입되었을때, oplog 로그로 모든 변경을 따라갈 수 있다면 바로 init sync 과정을 피할수 있다.

## Synchronization
MongoDB uses two forms of data synchronization: initial sync to populate new members with the full data set, and replication to apply ongoing changes to the entire data set.

### Initial Sync
replicaSet 의 member 에서 전체 데이터를 복사 하는 개념이다 (설정으로 `initialSyncSource` 지정가능)

- initialSourceSync 로 지정된 member 로 부터 모든 collection 을 카피
- index 카피
- initial sync 가 진행되는 동안 변경된 내역은 oplog 를 통해 catch-up

대신 traffic 을 받는 member 의 성능이 저하되므로, 백업파일을 통해 initial sync 수행후 oplog 를 catch-up 하는 방식으로 하는게 좋다

### Replication
Initial Sync 가 완료된 이후 ongoing changes 를 가져오는 방식을 의미한다 (secondary 에서 async/pull 해서 가져옴)

- Streaming
  - TBD  
- Multithreaded
  - TBD

### Elections
raft protocol 을 통한 리더선출을 수행합니다.