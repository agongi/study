### ReplicaSet

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.mongodb.com/manual/replication/
```

### Members

- Primary
  - 모든 write 연산은 primary 에서만 수행한다.
- Secondary
  - oplog 를 받아서, 데이터를 복제해서 고가용성 유지
- Arbiter
  - dataset 은 없고, vote 만 참여

### Oplog

- Primary 는 write 발생시 oplog 에 기록
- Secondary 는 주기적으로 oplog (default: 10s) 를 가져와서, async 하게 반영한다.
  - 자신의 `local.oplog.rs` collection 에 복사본을 만듬
  - default oplog 는 5% of disk size

> node 가 투입되었을때, oplog 로그로 모든 변경을 따라갈 수 있다면 바로 init sync 과정을 피할수 있다.

### Synchronization

#### Init Sync

장애등으로 secondary 가 stale 상태로 되었다면 (and/or add, replace member) init sync 과정이 필요하다.

- By removing its data and performing an initial sync
- copies all the data from one member of the replica set to another member.
- You can capture the data files as either a `snapshot` or a `direct copy`. However, in most cases you cannot copy data files from a running mongod instance to another because the data files will change during the file copy operation.
- 그후 oplog 로 따라갈수있을 만큼은 기존내역이 복사되어야함

> mongodump can't be used but snapshot only.

#### Replication

oplog 를 통해 primary -> secondary 로의 running changes apply 를 의미한다.

### High Availability

#### Election

일정시간 (heartBeat: 10s) 동안 member 가 없다면 election 을 거쳐서 새로운 리더를 선출한다.

복구된 primary 는 secondary 로 투입시 (startup2 상태로), initSync or oplog f/w 를 거쳐 secondary 로 재투입된다.

#### FailOver

Primary 가 죽어서 다시 replicaSet rejoin 시 (as a secondary), 새로 선출된 primary 에 미반영된 document 를 가지고 있다면 반영하지않고, 파일로 기록한다.