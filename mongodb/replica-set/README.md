### ReplicaSet

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.mongodb.com/manual/replication/
```

### Members

primary

secondary

arbiter

### Oplog

### Synchronization

init sync
- set member 중 아무놈에게 붙음 (보통 secondary 가 낫겠지, primary 는 이미 바쁨...)
- You can capture the data files as either a snapshot or a direct copy.
However, in most cases you cannot copy data files from a running mongod instance to another because the data files will change during the file copy operation.
- 그후 oplog 로 따라갈수있을 만큼은 기존내역이 복사되어야함
- You cannot use a mongodump backup for the data files: only a snapshot backup. 
For approaches to capturingㅇ a consistent snapshot of a running mongod instance, see the MongoDB Backup Methods documentation.

replication

### High Availability

Election

FailOver