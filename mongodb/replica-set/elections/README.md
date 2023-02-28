# Elections

```
@author: suktae.choi
- https://atharva-inamdar.medium.com/understanding-mongodb-oplog-249f3996f528
```

mongo 에서의 리더선출은 `raft protocol` 을 통해 수행합니다. 즉 기본적인 동작원리는 동일합니다.

## Rollbacks
(구)primary 가 secondary 로 rejoin 했을때 (금방 장애가 복구된 경우) (신)primary 보다 offset 이 높을 수 있습니다 (나만 가지고 있는 데이터가 존재함) 

이미 primary 는 선출된 상태이므로 (primary 기준으로 replication 해야함) 기존 데이터는 rollback 후 *.bson 파일에 로깅합니다

롤백을 방지하기 위해서는 `{w: "majority", j: true}` 로 설정하면 되지만 write 성능이 떨어지므로 선택이 필요합니다