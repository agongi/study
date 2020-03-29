## Transaction

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.confluent.io/blog/transactions-apache-kafka
- https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it
- https://gunju-ko.github.io/kafka/2018/03/31/Kafka-Transaction.html
- https://www.joinc.co.kr/w/man/12/Kafka/exactlyonce
```

## Transaction

- Producer가 이벤트 발행 시 transaction
- begin tx / send … / commit tx
- abort하더라도 앞서 보낸건 들어가긴 함. 커밋이 안됐다고 마킹함.
  - 컨슈머가 읽을 때 skip (`isolation.level=read_committed`으로 읽는 경우)

#### Atomic multi-partition writes

프로듀서가 쓸때

- ACK: all
- ISR: all

이면 모든 broker 로 confirm 된것이므로, write transaction 이 성공했다고 보면된다.

즉 이때시점에서 commit 발생.

#### Zombie fencing

#### Reading Transactional Messages

컨슈머가 읽을 때 skip (`isolation.level=read_committed`으로 읽는 경우)



## At Most Once

최대 1번 전달

- 유실가능
- 중복없음

## Exactly Once

정확히 1번 전달

- 유실없음
- 중복없음

## At least Once

적어도 1번이상 전달

- 유실없음
- 중복가능

