# Transactions

```
@author: suktae.choi
- https://docs.mongodb.com/manual/core/write-operations-atomicity/
```

몽고의 기본적으로 `document 단위의 atomic` 을 보장한다.

- (4.0) multi-document in replicaSet
- (4.2) multi-document in sharded-cluster
- (4.4) DDL in ALL 

## Read Concern
- local
- majority
- snapshot

"local": This is the default read concern level for transactions. It guarantees that the read operation will see the effects of any previously committed transaction on the same session.

"majority": This level guarantees that the read operation will see the effects of any previously committed transaction that was acknowledged by a majority of the replica set nodes.

"snapshot": This level guarantees that the read operation will see the effects of any previously committed transaction at a snapshot point in time.

## Read Preference
Multi-document transactions that contain read operations `must use read preference primary`. All operations in a given transaction must route to the same member.

## Write Concern
- w: 1
- w: majority

multi-document tx 에서 sharded-cluster 는 readConcern: snapshot 으로 해야한다 (새한님 내용 정리)
