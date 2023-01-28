### Available

```
ㅁ Author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern-available/
```

ReplicaSet 에 있는 node (지연이 가장 적은) 에서 data 를 가져온다.

### Orphan Document

아래의 흐름으로 고아객체를 가져 갈 수 있고, resultSet 에 중복이 발생 할 수 있음을 의미한다.

- sharded-cluster 에서 chunk balancing 을 위해 `chunk-migration` 이 수행된다.
- 마이그 완료된 chunk 는 source 에서 삭제를 수행한다.
  - balancer 는 성능을 위해 async 하게 수행할 수 있다.
- Secondary 에 삭제 반영을 위해 oplog 가 만들어졌지만, 아직 반영되지 않았다.
- 이때 SecondaryPreference - Available 로 조회가 들어왔다.
  - config 서버에서 target-index 를 타지 않아, 전체 샤드로 broadcast 수행일때
- 실제데이터가있는 A 샤드 + 삭제반영이 안된 B 샤드의 secondary (고아객체) 는 동일한 document 를 응답 할 수 있다.

> Local 은 Secondary 에서 응답할때도, shard-primary and/or config 서버에 항상 질의해서 consistency 보장한다.

### Available in replicaSet

Shard-Cluster 가 아닌 ReplicaSet 에서는 local 과 available 의 동작이 동일하다.