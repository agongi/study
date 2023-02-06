## Transaction

```
@author: suktae.choi
- https://www.confluent.io/blog/transactions-apache-kafka
- https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it
- https://gunju-ko.github.io/kafka/2018/03/31/Kafka-Transaction.html
```

## Transaction

- Producer 가 메세지 발행 시 begin transaction;
  - Transaction#open
  - Send messages (actually sent to broker & persisted)
    - 즉 일단 메세지는 무조건 brokder 로 전송되서 저장됨
  - Commit; or Rollback;
    - Once committed, sent messages are marked committed or not
- Abort 하더라도 앞서 보낸건 들어가긴 함. 대신 commit 으로 마킹되지 않음
- Consumer 는 committed 상태인 메세지만 가져감
  - `isolation.level=read_committed`
- 메세지 처리 후, consumer 는 commit 을 전송
- Broker 에서 offset 증가. (즉 offset 하위에 있는 모든 메세지는 consumed 로 처리)

이런식으로 트랜잭션이 진행됩니다. 아래에서는 Phase 별 동작을 설명하겠습니다.

### Atomic multi-partition writes

N 개의 파티션에 N 개의 메세지를 atomic 하게 보내는 개념입니다.

즉 Producer 에서 각 파티션별 메세지를 발행하고, 일괄 commit 을 하면 동시 반영되는 개념

### Reading Transactional Messages

Consumer 는 메세지를 읽을때, broker 에 메세지가 있지만 상태가 committed 인 메세지만 가져옵니다.

> `isolation.level=read_committed` 일때 해당함

### Read-Process-Write

Consumer 는 가져간 메세지의 처리 후, commit 을 broker 에 전송합니다.

그러면 메세지의 상태는 consumed 로 변경되고, offset 에 반영됩니다.

## Zombie fencing 

이 방식은 **zombie fencing** 을 수행합니다. transactional producer 는 bootstrap 때 부여되는 transaction.id 를 brokder 에 등록하고, 브로커는 해당 id 에서의 메세지만 유효하다고 판단합니다. 그리고 epoch (number) 을 increment 하게 관리합니다.

분산환경에서 일시적down/up 이 수행되면 그사이에 auto-start 정책에 따라 새로운 producer 가 생성되고, 그러면 동일한 transaction.id 를 지니고 message#pub 을 수행하면, 브로커는 내부에서 관리하는

- transaction.id
- epoch

을 확인후, 동일 id 의 epoch 보다 낮은 메타가 들어오면 discard 합니다. 이렇게 zombie instance (producer) 의 메세지가 차단되고 이 개념을 `zombie fencing` 이라고 합니다.

## Guarantees

### At Most Once (보통 이걸쓰지, 그래서 멱등성 유지가 중요)

Producer 가 보낸 메세지는 무조건 Broker 에서 유효한 메세지로 간주합니다.

timeout 등으로 producer 가 ack 를 받지못하면, retry 하는데 해당 메세지까지도 broker 는 저장합니다.

- 유실없음
- 중복가능

### Exactly Once

Producer 가 발행하는 message 는 고유의 identifier 를 가지고있습니다. 그래서 retry 메세지가 들어와도 Broker 는 id 로 식벽해서 중복은 discard 처리합니다.

- 유실없음
- 중복없음

### At least Once

그냥 비동기로 메세지 전송하는 개념입니다. ack 를 기대하지 않고 무조건 발행합니다.

- 유실가능
- 중복없음

