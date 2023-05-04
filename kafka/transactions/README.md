# Transactions

```
@author: suktae.choi
- https://www.confluent.io/blog/transactions-apache-kafka
- https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it
- https://gunju-ko.github.io/kafka/2018/03/31/Kafka-Transaction.html
```

exactly-once 는 transaction 을 지원한다는 의미이고, Producer 에서의 처리는 같습니다:

```java
public class Demo {
    KafkaProducer<String, String> producer = new KafkaProducer<>();

    public void send() {
        producer.initTransactions();
        producer.beginTransaction();

        try {
            producer.send(record);
            producer.flush();
            producer.commitTransaction();
        } catch (Exception e) {
            producer.abortTransaction();
        } finally {
            producer.close();
        }
    }
}
```

- producer
  - transaction#open
  - send messages
    - 메세지는 우선 brokder 로 전송되서 저장됩니다
  - commit; or rollback;
    - transaction commit 마킹하는 record 을 추가로 전송합니다
    - 롤백시 저장된 record 는 commit 으로 마킹되지 않습니다
- consumer
  - `isolation.level=read_committed` 설정이 된 consumer 는 committed 레코드만 fetch 합니다
  - 가져간 레코드 처리후 commit 을 보내 __consumer_offsets 에 기록합니다
  - consumer 입장에서 transaction 은 committed record 를 가져간다. 만 보장하고 exactly-once 를 보장하진 않습니다 (fetch 했지만 acks 실패 등)

즉 일반적인 사용성에서 kafka transaction 은

- producer: commit record 를 추가로 보내면서 exactly-once 보장
- consumer: coommit 된 record 만 fetch 까지만 보장 (중복가능)

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

### At least Once

그냥 비동기로 메세지 전송하는 개념입니다. ack 를 기대하지 않고 무조건 발행합니다.

- 유실가능
- 중복없음

### Exactly Once

Producer 가 발행하는 message 는 고유의 identifier 를 가지고있습니다. 그래서 retry 메세지가 들어와도 Broker 는 id 로 식벽해서 중복은 discard 처리합니다.

- 유실없음
- 중복없음