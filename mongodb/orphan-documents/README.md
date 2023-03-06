# Orphan Documents

```
@author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern/
- https://www.mongodb.com/docs/v6.0/reference/glossary/#std-term-orphaned-document
```

replicaSet 에서 primary 에서는 지워졌지만 `oplog 를 반영하는 secondary 에는 남아있는` document 를 orphan document 라고 합니다.

- sharded-cluster 에서 chunk balancing 을 위해 `chunk-migration` 수행
- 마이그 완료된 chunk 는 source 에서 삭제
  - delete 는 async 하게 수행됩니다
- Secondary 에 삭제 반영을 위해 oplog 가 만들어졌지만 아직 반영되지 않았고
- 이때 `SecondaryPreference - Available` 로 조회가 들어오면 발생합니다
  - config 서버에서 target-index 를 타지 않아, 전체 샤드로 broadcast 수행일때
  - readConcern: available 은 mongos 의 route info 를 확인하는 과정이 skip

> readConcern: local 은 Secondary 에서도 shard-primary or config 서버에 항상 질의해서 consistency 보장합니다