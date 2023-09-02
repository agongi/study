# Redis

```
@author: suktae.choi
- https://github.com/redis-study/redis-summary
- https://redis.io/docs/
```

### Index

- [Persistence](persistence)

### Blog

- [Transactions in Redis Cluster (Multi Nodes)](https://sauravomar01.medium.com/transactions-in-redis-cluster-muti-nodes-721da4919f66)
- [레디스 클러스터 Mget 명령은 어떻게 동작하는가?](https://brunch.co.kr/@springboot/359)
- [\[우아한테크세미나\] 191121 우아한레디스 by 강대명님](https://www.youtube.com/watch?v=mPB2CZiAkKM)

***

<img src='2.png' width="50%">

node 물리장비
slots 16384

단순한 get/set의 경우 초당 10만 TPS 이상 가능

Redis 서버는 slot range를 가지고 있기때문에 slot 단위로 데이터를 다른 서버로 전달하게 된다.

자기 slot 외에 데이터가 들어오면 -Moved {서버} 에러를 보낸다. 라이브러리는 이 에러를 받고 해당하는 서버로 옮겨 줘야한다.

## 저장

<img src='1.png' width="50%">

- AOF
  - 커맨드를 받을 때마다 파일에 추가
  - 실시간성 보장 (always/everysec)
  - [Log rewriting](https://redis.io/docs/management/persistence/#log-rewriting)
    - kafka log compact 와 동일처리 (copy-on-write 방식)
- RDB
  - 주기적으로 스냅샷을 통채로 저장
  - 큰 데이터 집합을 복구할 때, 복구 속도는 RDB가 AOF보다 빠르다. RDB는 전체 데이터베이스에서 발생하는 모든 변경을 재실행할 필요가 없기 때문이다.

RDB 으로 해당 시점까지의 스냅샷으로 복구후, 이후 데이터는 AOF 를 사용하는 방식으로 응용 가능합니다

## [복제](https://buildatscale.tech/redis-replication/)

<img src='5.png' width="50%">

master -> slave 간의 복제를 의미합니다. (async)

https://redis.io/docs/management/replication/

```
복제 ID 설명
이전 섹션에서는 두 인스턴스에 동일한 복제 ID와 복제 오프셋이 있는 경우 정확히 동일한 데이터가 있다고 설명했습니다. 그러나 복제 ID란 무엇이며, 인스턴스에 실제로 두 개의 복제 ID(기본 ID와 보조 ID)가 있는 이유를 이해하는 것이 유용합니다.

복제 ID는 기본적으로 데이터 세트의 특정 기록을 표시합니다. 인스턴스가 마스터로 처음부터 다시 시작될 때마다 또는 복제본이 마스터로 승격될 때마다 이 인스턴스에 대해 새 복제 ID가 생성됩니다. 마스터에 연결된 복제본은 핸드셰이크 후 복제 ID를 상속합니다. 따라서 동일한 ID를 가진 두 인스턴스는 동일한 데이터를 보유하지만 다른 시점에 있을 수 있다는 사실에 의해 연결됩니다. 이것은 특정 기록(복제 ID)에 대해 누가 가장 업데이트된 데이터 세트를 보유하고 있는지 이해하기 위한 논리 시간 역할을 하는 오프셋입니다.

예를 들어, 두 인스턴스 A와 B가 동일한 복제 ID를 갖지만 하나는 오프셋 1000이고 다른 하나는 오프셋 1023이면 첫 번째 인스턴스에는 데이터 세트에 적용되는 특정 명령이 없습니다. 는 것을 의미합니다. 또한 A는 약간의 명령을 적용하기만 하면 B와 정확히 같은 상태에 도달할 수 있음을 의미합니다.

Redis 인스턴스에 두 개의 복제 ID가 있는 이유는 복제본이 마스터로 승격되기 때문입니다. 장애 조치 후 승격된 복제본은 이전 복제 ID가 이전 마스터의 것이었으므로 기억해야 합니다. 이 방법으로 다른 복제본이 새 마스터와 동기화할 때 이전 마스터 복제 ID를 사용하여 부분 재동기화를 시도합니다. 복제본이 마스터로 승격되면 보조 ID가 기본 ID로 설정되고 이 ID 전환이 발생했을 때의 오프셋이 기억되기 때문에 이는 예상대로 작동합니다. 새 기록이 시작되므로 나중에 새 임의의 복제 ID가 선택됩니다. 연결하는 새 복제본을 처리할 때 마스터는 해당 ID와 오프셋을 현재 ID와 보조 ID 모두와 비교합니다(안전을 위해 지정된 오프셋까지). 즉, 이는 장애 조치 후 새로 승격된 마스터에 연결하는 복제본이 전체 동기화를 수행할 필요가 없음을 의미합니다.

마스터로 승격된 복제본이 장애 조치 후 복제 ID를 변경해야 하는지 궁금한 경우 네트워크 파티션으로 인해 이전 마스터가 여전히 마스터 역할을 할 수 있습니다. 동일한 복제 ID를 보유한다는 것은 두 개의 임의 인스턴스의 동일한 ID와 동일한 오프셋은 동일한 데이터 세트를 가짐을 의미합니다.
```

## 트랜잭션

multi - exec

클러스터에서 트랜잭션 미지원 이유

### 레디스 클러스터

https://backtony.github.io/redis/2021-09-03-redis-3/ 

<img src='3.png' width="50%">

여러 대의 서버에 분산 저장할 때 각 슬롯 당 데이터를 일정한 단위로 분류하여 저장할 때 사용됩니다. 

3대의 Redis 서버가 구축되어 있는 환경에서 첫 번째 서버에는 0 - 5460, 두 번째 서버에는 5461 - 10922, 세번째는 10923 - 16384 으로 분산되어 저장합니다

### 레디스 센티널

https://backtony.github.io/redis/2021-09-02-redis-2/

<img src='4.png' width="50%">