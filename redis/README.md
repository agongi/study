# Redis
```
https://github.com/redis-study/redis-summary
https://redis.io/docs/
```
### Index
- [Persistence](persistence)

### Blog
- [레디스 클러스터 Mget 명령은 어떻게 동작하는가?](https://brunch.co.kr/@springboot/359)
- [\[우아한테크세미나\] 191121 우아한레디스 by 강대명님](https://www.youtube.com/watch?v=mPB2CZiAkKM)

***
<img src='2.png' width="50%">

```
[Client]
   │
   │ (key)
   ▼
[슬롯 계산: HASH(key) % 16384]
   │
   ▼
[슬롯 → 담당 노드 확인]
   │
   ▼
[선택된 Redis 노드]
   │
   └─> 해당 노드의 해시 테이블에 접근
         ├── key1 → value1, value1-1, value1-2  (LinkedList)
         ├── key2 → value2                     (LinkedList)
         └── ...
```
클라이언트에서 KEY 를 기반으로 SLOT > NODE 를 계산해서 해당 노드에 명령을 전달합니다.

서버는 잘못된 명령이 온다면 (해당 SLOT 을 저장하지 않은 노드) MOVED 명령을 보내고 > 클라이언트는 캐시 갱신후 다른 노드에 요청을 다시 합니다

> 단순한 GET/SET 요청은 100,000/s 정도를 처리할 수 있습니다

## 영속화
IN-MEMORY DB 이지만 주기적으로 영속화 합니다:
<img src='1.png' width="50%">

- AOF
  - CUD 커맨드를 받을 때마다 `실시간으로` append 하여 저장 (appendonly.aof)
    - [Log rewriting](https://redis.io/docs/management/persistence/#log-rewriting)
    - kafka 의 `cleanup.policy=compact` 처럼 최신1개만 유지
- RDB
  - `일정 시간 간격`으로 메모리 전체 스냅샷 저장 (dump.rdb)

큰 데이터를 복구할 때, RDB 가 AOF 보다 빠릅니다. (스냅샷을 그대로 올리면 되므로)

> RDB 으로 해당 시점까지 스냅샷 복구후, 이후 변경사항은 AOF 를 사용하는 방식으로 응용 가능

## [복제](https://redis.io/docs/reference/cluster-spec/#write-safety)
<img src='5.png' width="50%">

- master 는 1s 단위로 AOF 에 기록된 command 를 replica 에 전달합니다
  - 일반적인 RDB 쓰기지연 (기본값: 1s) 정도의 수

## [파이프라이닝](https://redis.io/docs/latest/develop/use/pipelining/)
Redis pipelining is a technique for improving performance by `issuing multiple commands at once` without waiting for the response to each individual command.

```java
redisTemplate.execute(connection -> {
    try {
        for (int i = 0; i < keys.size(); i++) {
            redisTemplate.opsForValue().set(keys.get(i), values.get(i));
        }
        return null; // 결과 반환
    } finally {
        connection.flush(); // 파이프라인에 담긴 명령 실행
    }
});
```

## [트랜잭션](https://redis.io/docs/interact/transactions/)
```
> MULTI (== begin)
OK
> INCR foo
QUEUED
> INCR bar
QUEUED
> EXEC (== commit) or DISCARD (== rollback)
1) (integer) 1
2) (integer) 1
```

- tx 에서 실행되는 명령은 `순차적으로 실행` 됩니다
  - 즉 `다른 명령이 중간에 실행 될 수 없음`
- EXEC 명령은 queueing 된 명령의 실행을 트리거링 합니다
  - tx 실행도중 client-connection 이 끊어져도 (큐잉된) command 는 서버에서 실행됩니다
  - tx 실행도중 일부 커맨드가 실패해도 나머지 명령은 실행됩니다
- 레디스는 `롤백을 지원하지 않습니다`
  - 해당 명령을 순차적으로 (중간에 다른 커맨드를 실행하지 않고) 실행한다는 의미입니다
  - RDB 의 트랜잭션처럼 롤백을 지원하지는 않습니다
- [클러스터 환경에서는 tx 를 지원하지 않습니다](https://sauravomar01.medium.com/transactions-in-redis-cluster-muti-nodes-721da4919f66#46e3)
  - 모든 node 를 global-lock 잡아야해서, 몽고DB 에서도 지양

## 클러스터링
### `클러스터`
https://backtony.github.io/redis/2021-09-03-redis-3/ 

<img src='3.png' width="50%">

여러 대의 서버에 분산 저장할 때 각 슬롯 당 데이터를 일정한 단위로 분류하여 저장할 때 사용됩니다. 

3대의 Redis 서버가 구축되어 있는 환경에서 node1: 0 - 5460, node2: 5461 - 10922, nod3: 10923 - 16384 으로 분산되어 저장합니다

### 센티널 (HA)
https://backtony.github.io/redis/2021-09-02-redis-2/

<img src='4.png' width="50%">

## [실시간 메세지 (PUSH)](https://techblog.lycorp.co.jp/ko/building-a-messaging-queuing-system-with-redis-streams)
### [Pub/Sub](https://docs.spring.io/spring-data/redis/reference/redis/pubsub.html)
- 영속성
  - Broadcasting 기반으로 `fire-and-forget`
  - 유실된 메세지의 replay 불가능
- 수신단위
  - 개별 subscriber
- 정합성
  - 발행된 메세지는 replay 되지 않고 `유실`

### [Streams](https://docs.spring.io/spring-data/redis/reference/redis/redis-streams.html)
- 영속성
  - `영속적`으로 가지고 있음
  - 저장된 메세지는 컨슈머그룹 마다 관리되는 offset 을 이용해서 replay 가능
- 수신단위
  - consumer group
  - group 으로 묶을수 있어서 동일 그룹에는 중복 메세지 방지가능
- 정합성
  - XACK 를 통해 수신여부 확인
  - 일정기간 동안 XACK 가 오지 않으면 저장하고 있던 마지막 offset 기반으로 재전송

## [Spin-Lock](https://hdbstn3055.tistory.com/271)
SET command 를 통해 (timeout 설정하면서) 값을 세팅하고, 적절한 interval 로 체크하는 구현방식 입니다.

```java
// 사용방법
public T submit(Callable<T> callable) {
    if (tryLock()) {
        try {
            return callable.call();
        } catch (Exception e) {
            // logging
            throw new Exception();
        } finally {
            unlock(cacheName, key);
        }
    }
}

// 간단한 구현체
private boolean tryLock() {
  try {
    while (true) {
      boolean acquired = redisTemplate.opsForValue().setIfAbsent("KEY", "VALUE", "TIMEOUT");
      // acquired?
      if (acquired) {
        return true;
      }
      // timeout
      if (isTimeout(startTime, acquireTimeoutInMillis)) {
        return false;
      }
      // interval
      TimeUnit.MILLISECONDS.sleep(DEFAULT_TIMEOUT_MILLIS);
    }
  } catch (Exception e) {
    return false;
  }
}
```