## Kafka

```
ㅁ Author: suktae.choi
ㅁ References:
- https://kafka.apache.org/documentation
- https://github.com/kafkakru/meetup/tree/master/conference/1st-conference
- https://www.popit.kr/author/peter5236
- https://bysssss.tistory.com/46
```

#### Index

- APIs
- [Schema Registry](https://medium.com/@gaemi/kafka-%EC%99%80-confluent-schema-registry-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EC%8A%A4%ED%82%A4%EB%A7%88-%EA%B4%80%EB%A6%AC-1-cdf8c99d2c5c)
- Kafka Stream vs KSQL

***

### Kafka

#### Topic/Partition

토픽은 N 개의 파티션으로 분산됨

파티션단위의 순서는 보장됨

1개의 파티션은 consumer group 단위로, 그룹안에 1개의 consumer 만 구독가능

> 동시처리 방지위해

#### Offset

각 consumer group 의 offset 을 토픽단위로 관리함

> 예전에는 zookeeper 에서, 지금은 kafka 자체가 저장

#### Replication

해당 토픽의 카프카 리더가 R/W 를 모두 담당함

브로커의 ISR (In Sync Replica) 그룹안에서 리더가 선출됨

> 리더는 zookeeper ephemeral node 를 보고, hostname 등의 판단규칙으로 선출

- Leader - 주기적으로 heartbeat 을 보내 응답하지 않는 follower 를 ISR 그룹에서 제외
- Follower - 주기적으로 Leader 의 data pull

> 즉 ISR 내에서는 fresh 보장이되므로, 아무나 리더가 되도 상관없다고 판단

![1](images/1.png)

전면장애시 정책은 설정가능

- 마지막 Leader 의 복구를 기다림 (복구느림, 유실적음)
- ISR 안에서의 아무나 살아나면 leader 로 (복구빠름, 유실존재)

### Producer

#### Acks

- 0: no ack from leader
- 1: ack from leader
- all: ack from all ISR members 

### Consumer

#### Consumer Group

Consumer 가 동적으로 변경되면서 rebalance 발생함

- 그때 시점에, 해당 consumer group 에서는 데이터 처리하지 않음

#### Commit

Consumer group 에서 kafka 에 offset 을 기록하는 과정

- auto commit: time-interval 로 주기적으로 commit 해버림 (누락가능)
- manual commit: logic 에서 #commit 호출 (중복가능)

> 토픽단위의 멱등성이 유지되는게 중요. 그리고 manual-commit 으로 당연히 가야지