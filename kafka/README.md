# Kafka

```
@author: suktae.choi
- https://kafka.apache.org/documentation
- https://docs.confluent.io/kafka/introduction.html
- https://www.conduktor.io/kafka/
- https://github.com/kafkakru/meetup/tree/master/conference/1st-conference
- https://www.popit.kr/author/peter5236
- https://bysssss.tistory.com/46
```

### Index

- [Kafka Stream](kafka-stream)
- [Kafka Connect](kafka-connect)
- [MirrorMaker 2.0](mm2)
- [Schema Registry](schema-registry)
- [KSQL](ksql)
- [Transactions](transactions)

### Blog

- [Consumer – Push vs Pull approach](https://blog.knoldus.com/kafka-consumer-push-vs-pull-approach/)
- [Schema Registry](https://medium.com/@gaemi/kafka-%EC%99%80-confluent-schema-registry-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EC%8A%A4%ED%82%A4%EB%A7%88-%EA%B4%80%EB%A6%AC-1-cdf8c99d2c5c)

***

## 기본 개념

### Topic/Partition

1개의 토픽은 N 개의 파티션으로 분산되어 저장됩니다.

파티션은 Broker 에 로그파일 (==segment) 로 저장되며 파티션 리더만을 통해 CRUD 가 발생합니다. 즉 Producer, Consumer 는 파티션 리더와 통신합니다.

> 파티션단위의 순서는 보장됨

<img src='1.png' width='75%'>

### Page cache

카프카는 모든 IO 에 OS 레벨의 page cache 를 활용합니다. (별도로 카프카 내부에서의 캐싱 없음)

<img src='1-1.png' width='75%'>

https://docs.confluent.io/platform/current/kafka/deployment.html#memory 의 가이드에 따르면

- 카프카 자체에 대한 -Xmx -Xms 는 5G 정도면 충분
- 나머지는 모두 OS 가 사용하도록 (page cache) 메모리는 충분히 여유있게 유지

해야합니다.

### Zero copy (== Direct memory or DMA)

page cache 를 통해 memory 에 있는 record 는

- producer -- broker
- broker -- consumer

간의 통신에서 zero-copy 를 통해 수신/전송 됩니다. 이를 통해 JVM heap 의 사용률을 줄일 수 있고 불필요한 복사비용이 감소합니다.

### Segment (== file)

브로커에 저장되는 레코드의 (물리적인) 로그파일 입니다.

- 브로커는 파티션의 모든 세그먼트에 대해 각각 하나의 열린 파일 핸들러를 유지 합니다
- 따라서 OS File Descriptor 는 [충분한 숫자](https://docs.confluent.io/current/kafka/deployment.html#file-descriptors-and-mmap) 로 설정해야 합니다

```bash
# current opened socket counts
$ find /{kafka_home} -name '*index' | wc -l

# FD increased
$ echo 'vm.max_map_count=262144' >> /etc/sysctl.conf
# apply
$ sysctl -p
```

### Log Retention

Record 를 저장하는 파일의 보관주기는 아래와 같습니다:

- 시간: 특정시간이 지난 파일 삭제 (default. 7-days)
- 사이즈: 특정사이즈가 오버되면 파일 삭제 (default. 1G)
- 주기: retention 체크 주기 (default. 5-mins)

### Log Compaction

Log compaction ensures that Apache Kafka will always `retain at least the last known value` for `each message key` within the log of data for a `single topic partition`.

<img src='1-1.png' width='75%'>

- kafka-key 를 기준으로 message compaction 을 진행하므로, compaction 사용시 key 는 필수값 입니다
  - record 의 key 는 원래 비필수
- 각 파티션에서의 unique 만 보장합니다 (global unique 하지 않음)
  - 그에 따라 파티션 rebalancing 으로 개수가 증가하는 경우 중복키가 발생 할 수 있습니다

```json
log.cleanup.policy=compact
```

## Broker

### [Replication](https://docs.confluent.io/kafka/design/replication.html)

카프카는 파티션 리더가 모든 CRUD 를 담당하므로, 팔로어는 주기적으로 segment 을 fetch 해서 replication 을 수행합니다.

```
// TODO - 해당 과정에 대한 그림 필요

우선 데이터 보내고 나중에 commit 유무 알려줌 (next fetch 에서)
리더는 모든 ISR 이 복제를 완료한 데이터만 (즉 commit 된 데이터를 의미함) consumer 가 읽어가도록 보장한다
```

replication.factor 에 설정된 수치만큼 replication 이 되고, out-of-sync 가 아닌 팔로어를 ISR (InSyncReplica) 로 관리합니다.

- leader: 주기적으로 heartbeat 을 보내 응답하지 않는 follower 를 ISR 그룹에서 제외
- follower: 주기적으로 Leader 의 data fetch

### Controller

[Controller Broker](https://www.slideshare.net/ConfluentInc/a-deep-dive-into-kafka-controller) 는 브로커 중 하나가 임의로 선정 됩니다.

- 목적: (브로커) 장애시 해당 브로커에 속하던 `파티션 리더 선출`
- 플로우: ISR 에서 raft 를 통해 리더를 선출합니다

### Coordinator

[Coordinator Broker](https://kafka.apache.org/documentation/#impl_offsettracking) 는 브로커 중 하나가 임의로 선정 됩니다.

- 목적: (컨슈머) 장애시 해당 파티션을 처리하는 `다른 consumer-group 선정`
- 플로우: 기본적으로 heartbeat 로 체크하고 record polling, offset commit 이 오면 heartbeat 를 받았다고 판단합니다

## Producer

메세지를 전송하는 단위 입니다.

### send 흐름

<img src='3.png' width='75%'>

- send
- partitioner
- accumulator
- sender

// TODO - 각 kafka options 의미 설명

### Acks

<img src='4.png' width='75%'>

- 0: no ack from leader (== async)
- 1: ack from leader
- all: ack from all ISR members

### ProducerRecord

```java
public class ProducerRecord<K, V> {
    private final String topic;
    private final Integer partition;
    // metadata 저장. key-value pair
    private final Headers headers;
    // (optional) 파티션을 결정하는 식별단위 (key 의 hash 로 partition 결정, 없으면 RR)
    private final K key;
    private final V value;
    private final Long timestamp;

    // ...
}
```

### send 유형

- at-least once
  - acks 를 기다리고 실패시 재전송
- at-mose once
  - acks 를 기다리지 않음
- exactly once (== transaction)
  - PID (producerID) & sequence 의 조합
  - `enable.idempotence=true`

exactly-once 는 transaction 을 지원한다는 의미이고, Producer 에서의 처리는 같습니다:

```java
KafkaProducer<String, String> producer=new KafkaProducer<>(configs);

    producer.initTransactions();
    producer.beginTransaction();

    try{
    producer.send(record);
    producer.flush();
    producer.commitTransaction();
    }catch(Exception e){
    producer.abortTransaction();
    }finally{
    producer.close();
    }
```

- send 된 message 는 broker 에 저장 (offset++)
- 그후 commit 수행 (offset++)
  - 즉 transaction committed 마킹하는 record 가 추가로 전송됨
- consumer 는 `read_committed` 으로 설정하고, latest-commit 마킹 이전의 record 만 fetch
  - consumer 는 commit 된 메세지를 가져간다. 까지만 보장하고 exactly-once 를 보장하진 않습니다 (fetch 했지만 acks 실패 등)

즉 일반적인 사용성에서 kafka transaction 은

- producer: commit record 를 추가로 보내면서 exactly-once 보장
- consumer: coommit 된 record 만 fetch 까지만 보장 (중복가능)

## Consumer

메세지를 소비하는 단위 입니다.

### fetch 흐름

<img src='5.png' width='75%'>

- fetcher
- coordinator
- assigner

// TODO - 각 kafka options 의미 설명

### Consumer Group

consumer 는 특정 consumer-group 에 속하고, 그룹은 group-id 로 구분됩니다. 컨슈머그룹은 subscribe 하는 파티션의 offsets 을 `__consumer__offsets` 토픽으로 관리합니다.

- consumer-group:partition:offsets 으로 관리

### Rebalancing

그룹안에서 Consumer 추가/삭제시 rebalancing 발생

- 리밸런싱이 일어나는 동안은 STW

## Advanced

### ACID

- producer: replication.factor (즉 ISR) 을 만족하면 그것을 commit 으로 간주합니다
  - transaction 을 사용한다면 -> commit record 를 명시적으로 한번 더 보내는 과정이 추가
- consumer: polling 시 커밋된 메세지만 가져옵니다 (== 모든 ISR 에 동기화된 record)
  - transaction 을 사용한다면 -> commit 마킹된 record 만 pull

## 가용성 vs 내구성

`unclean.leader.election.enable` 옵션을 통해 결정됩니다.

- false: ISR 에서만 leader 를 선출합니다
  - 가용성 낮음
  - 내구성 높음
- true: ISR 가 없다면 (== out-of-sync) replicas 중에서 리더를 선출한다.
  - 가용성 높음
  - 내구성 낮음