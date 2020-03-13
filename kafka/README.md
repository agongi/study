## Kafka

```
ㅁ Author: suktae.choi
ㅁ References:
- https://kafka.apache.org/documentation
- https://github.com/kafkakru/meetup/tree/master/conference/1st-conference
- https://www.popit.kr/author/peter5236
```

#### Index

- APIs
- [Schema Registry](https://medium.com/@gaemi/kafka-%EC%99%80-confluent-schema-registry-%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EC%8A%A4%ED%82%A4%EB%A7%88-%EA%B4%80%EB%A6%AC-1-cdf8c99d2c5c)
- Kafka Stream vs KSQL

***

### Kafka

#### Topic/Partition

토픽은 파티션으로 나뉘어짐

파티션단위의 순서는 보장됨

1개의 파티션은 consumer group 단위로, 그룹안에 1개의 consumer 만 구독가능

> 동시처리이슈 방지위해

#### Leader

리더선출은 zookeeper

리더선출과정 설명...

카프카리더가 R/W 를 모두 담당함

### Producer

#### Acks

- 0
- 1
- 2

### Consumer

#### Consumer Group

