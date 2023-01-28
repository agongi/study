### Majority

```
ㅁ Author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern-majority/
```

해당 ReplicaSet 의 과반수 이상이 응답한 데이터를 사용한다.

즉 모든 node 에 대해서 과반수 이상의 응답 확보를 위해 전체 read 가 발생한다.

> This behavior impacts cost of read performance.

### PSA (Primary-Secondary-Arbiter) model

PSA 에서는 과반수 이상을 disable 할 수 있다.

아래의 영향으로 장애 발생을 유발하기 때문이다:

> Arbiter 는 vote 만 할뿐, data 에는 관여하지 않는다.

- Set 에서 1대가 down
- Read 발생
  - majority 확보를 하지 못해 모두 타임아웃까지 대기
  - 장애