# Replication
```
https://dev.mysql.com/doc/refman/8.0/en/replication-multi-source.html
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