### Chunk

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.mongodb.com/manual/tutorial/migrate-chunks-in-sharded-cluster/
- https://docs.mongodb.com/manual/core/sharding-data-partitioning/
```

### Balancer

The balancer process is responsible for redistributing the chunks of a sharded collection evenly among the shards for every sharded collection.

To address uneven chunk distribution for a sharded collection, the balancer [migrates chunks](https://docs.mongodb.com/manual/core/sharding-balancer-administration/#) from shards with more chunks to shards with a fewer number of chunks.

### Chunk Migration

1개의 Chunk 는 최대 `64MB` (by default) 이다.

- Balancer 가 판단하기에, 청크 불균형이라면 마이그레이션을 수행한다.
  - 절반으로 잘라서 다른 샤드로 분배함
- 마이그 도중 CUD 는 Source 가 처리한다.
- Target 은 들어오는 Chunk 에 대한 인덱스 생성
- 복사시작
- 복사가 완료되면, 그동안의 변경분 delta 를 적용 by oplog (sync)
  - Blocking?
- 완료후 Source 는 config 에 접근해서 shard-key 정보 갱신 (이젠 target 으로 라우팅되도록)
- Source 에서 Mig 된 청크는 삭제처리
  - 삭제는 async 하게 수행된다. 즉 source-secondary 에서는 `불일치 (== Orphan Document)` 가 발생 할 수 있다.
