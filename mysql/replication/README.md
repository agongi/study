# Replication
```
https://velog.io/@dangdang/MySQL-%EB%B3%B5%EC%A0%9C
```

source server: 생성된 binary log 를 replica server 로 전달
replica server: 저장 (로컬 디스크에 저장) - source server 의 id/pw 등의 정보를 가지고 있음
replica server: 동기화 (리포지토리에 반영)

- 바이너리 로그(Binary Log): MySQL 서버에서 발생하는 모든 변경 사항이 기록되는 곳
- 릴레이 로그(Relay Log): 레플리카 서버에서 소스 서버의 바이너리 로그를 읽어 들여 로컬 디스크에 저장해둔 파일

<img src="1.png" width="75%">

## 식별자 타입
즉 어디까지 수행했어? 를 tid 기반으로 구분 필요한데 식별자로 어떤 값을 사용할지에 대한 고민

<img src="2.png" width="75%">

### Binlog:offset
ROW 기반 바이너리 로그 포맷을 사요한다면 데이터 자체가 복제되므로 해당 방식도 이슈없지만 아래의 단점이 존재합니다:
- 각 서버마다 binlog:offset 기반은 file rotation 정책 등에 따라 다를 수 있는데 source 의 값을 replica 에서 그대로 사용 할 수 없음
  - 따라서 어느 트랜잭션까지 수행되었는지는 알기 어려움 (MM2 처럼 각자의 offset 은 다를수 있으므로 그 GAP 을 관리하는 별도의 토픽(관리)가 필요함)

### GTID (== Global Transaction ID)
GTID 활성화 전, binlog_format = ROW 추천

## 바이너리 로그 포맷
### Statement 기반
구문이 replication 으로 들어가면 다른 결과가 나올 수 있음. (ex. P.K 가 다르게 생성되거나 NOW() 의 결과 등이 다름)
즉 값 그 자체를 복사해야 불일치 미발생 / 대신 복제 사이즈가 작음

### Row 기반
복제 사이즈가 큼 (그만큼의 복제지연 가능). 대신 불일치 가능성 없음
압축도 고려가능

```
binlog 형식은 2가지 방식이 있습니다.
- ROW 포맷
- STATEMENT 포맷

MySQL 8.x 부터 binlog 의 포맷이 ROW 가 기본값이 되었고 이해한 내용을 정리하면
- insert into select ... 처럼 동적인 결과로 insert 쿼리가 발생하면 (그리고 NOW() 문구도 포함해서)
- master/replicas 의 결과가 100% 동일하다고 보장할 수 없습니다 
```

## 동기화 방식
### Asynchronous
<img src="3.png" width="75%">

### Semi-Synchronous
<img src="4.png" width="75%">

### Synchronous
실제 relay 받고 replica 에서 commit 완료 까지 대기

## MSR (Multi Source Replication)
샤딩을 해서 source 가 여러개인 경우를 의미 (N개의 source -> 1개의 replica)
그런데 각각의 샤딩 source 에 각 replication 이 존재하는게 낫지않나?

## MMM (Mysql Multi-master replication Manager) & DNS
- MMM Agent
  - 각 DB 서버에서 MMM 모니터와 통신하여 서버 상태 정보를 전송하고, MMM 모니터의 지시에 따라 작업을 수행
- MMM Monitor
  - 각 서버의 상태를 모니터링하고, 장애 발생 시 자동 복구 프로세스를 실행
- VIP (DNS)
  - MMM Monitor 를 통해 복구 프로세스 실행되어 VIP 변경이 필요한 경우 DNS 를 갱신
- 클라이언트
  - DNS 를 통해 VIP 획득하여 DB 접속
  - Java 의 경우 `networkaddress.cache.ttl=?` 옵션으로 JVM DNS 캐시시간 설정 (DNS 부하와 failover 시간을 고려하여 설정)

<img src="5.png" width="75%">