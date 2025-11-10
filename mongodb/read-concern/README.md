# Read Concern

```
https://docs.mongodb.com/manual/reference/read-concern/
https://www.youtube.com/watch?v=14BwYGaohhI
```

## local
해당 node 기준으로 up-to-date 인 data 를 반환한다
- majority 가 보장되지 않는 데이터 반환 가능
- available `in current node`
- (shard cluster) config server 와 통신해서 route info 확인 O

> orphan document 가 발생하지 않음

## available (shard cluster only)
최소 1개의 노드에서 응답한 데이터까지 반환한다
- majority 가 보장되지 않는 데이터 반환 가능
- available `at-most one node`
- (shard cluster) config server 와 통신해서 route info 확인 X

## majority
과반수 이상의 replicaSet 이 응답한 데이터를 반환합니다.
- `primary/secondary 상관없이 과반이상` 데이터 조회

## linearizable
과반수 이상의 replicaSet 이 응답한 데이터를 반홥합니다.
- `primary 를 통해서만 과반이상` 데이터 조회

## snapshot
다중 문서 트랜잭션(multi-document transactions) 내에서 사용합니다. (트랜잭션 시작 시점의 스냅샷을 제공하여, 트랜잭션 내내 일관된 데이터를 읽도록 보장)
- RDB 의 repeatable-read 와 동일
