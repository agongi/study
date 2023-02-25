# Sharded Cluster

```
@author: suktae.choi
- https://docs.mongodb.com/manual/sharding/
- https://docs.mongodb.com/manual/core/sharding-shard-key/
- https://docs.mongodb.com/manual/tutorial/migrate-chunks-in-sharded-cluster/
- https://docs.mongodb.com/manual/core/sharding-data-partitioning/
```

<img src="1.png" width="50%">

## ShardKey
### Hashed Sharding

ShardKey 를 Hash 해서 샤딩하는 방식이다.

샤드키의 갯수만큼 해시의 테이블이 관리된다.

> 즉 데이터가 많아지면 Config 서버의 데이터도 linear 하게 증가하므로, 성능이 같이 느려진다.

### Ranged Sharding

해시를 하지않고, 범위로 짤라서 샤딩한다.

즉 샤드키가 close 한 document 일수록 동일 샤드에 있는 확률이 높다.

대신 부하가 특정샤드에만 집중될 수 있다. (분산되지 않음)

- linear 하게 증가하는 값이 샤드키라면, 일정범위까지 동일 샤드 (청크) 로 몰릴것이다.
- 즉 write 가 특정샤드로만 집중된다.

## Chunk
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

점보청크정리