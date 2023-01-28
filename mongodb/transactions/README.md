### Transactions

```
ㅁ Author: suktae.choi
- https://docs.mongodb.com/manual/core/write-operations-atomicity/
```

몽고의 기본적인 `lock 범위는 document` 이다.

> sub-document CUD 발생시에도, document lock 이 잡힌다.

### Yield

slow-query 라면, IO 작업동안 리소스양보 (CPU)

### ACID

#### Single-Document

4.0 이전까지는 single-document 에 대해서만 ACID 를 보장했다.

#### Multi-Document

- (replicaSet) since 4.0
- (sharded-cluster) since 4.2

> client 에서 tx 를 명시적으로 여는 코드사용이 필요함

```java
try (ClientSession session = client.startSession()) {
  session.startTransaction();
  
  try {
    mongoTemplate.insert(..);
    mongoTemplate.insert(..);
    
    session.commitTransaction();
  } catch (Exception e) {
    session.abortTransaction();
  }
}
```

### Write Conflicts

CUD 가 1개의 document 에 몰렸을때의 동작:

- write retry until timeout

> 다른 RDB 는 block 상태에서 대기함, 즉 지속적인 retryable 에 따른 오버헤드 발생