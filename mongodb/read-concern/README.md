# Read Concern

```
@author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern/
```

## local
해당 node 기준으로 up-to-date 인 data 를 반환한다

- majority 가 보장되지 않는 데이터 반환 가능
- available in current node

## available
최소 1개의 노드에서 응답한 데이터까지 반환한다

- majority 가 보장되지 않는 데이터 반환 가능
- available at-least one node

replicaSet 인 경우 local, available 의 동작은 동일하다

## majority
과반수 이상의 replicaSet 이 응답한 데이터를 반환합니다.
현재 w:majority 작업이 있는경우, 대기하지 않고 바로 반환합니다.

## linearizable
과반수 이상의 replicaSet 이 응답한 데이터를 반홥합니다.
현재 w:majority 작업이 있는경우, 완료까지 대기후 응답에 포함해서 반환합니다.

## snapshot
RDB 의 repeatable-read 와 동일함
