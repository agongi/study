## Lock

```
ㅁ Author: suktae.choi
ㅁ References:
```

몽고의 기본적인 `lock 범위는 document` 이다.

> sub-document CUD 발생시에도, document lock 이 잡힌다.

### Yield

slow-query 라면, IO 작업동안 리소스양보 (CPU)

### ACID

#### Multi-Document ACID

- (replicaSet) since 4.0
- (sharded-cluster) since 4.2

> client 에서 (서버와동일한 driver, tx 를 명시적으로 여는 코드사용) 이 병행되어야함

### Write Conflicts

CUD 가 1개의 document 에 몰렸을때의 동작:

- write 를 계속 retry until timeout

> 다른 RDB 는 block 상태에서 대기함, 즉 지속적인 retryable 에 따른 오버헤드 발생