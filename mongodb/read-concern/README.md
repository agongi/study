# Read Concern

```
@author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern/
- https://www.youtube.com/watch?v=14BwYGaohhI
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
현재 w:majority 작업이 있는경우, 대기하지 않고 바로 반환합니다.

## linearizable
과반수 이상의 replicaSet 이 응답한 데이터를 반홥합니다.
현재 w:majority 작업이 있는경우, 완료까지 대기후 응답에 포함해서 반환합니다.

## snapshot
RDB 의 repeatable-read 와 동일함
