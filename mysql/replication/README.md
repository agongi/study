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

GTID 활성화 전, binlog_format = ROW 추천

<img src="2.png" width="75%">

## GTID
Statement 기반 바이너리 로그 포맷: 구문이 replication 으로 들어가면 다른 결과가 나올 수 있음. 즉 값 그 자체를 복사해야 불일치 미발생 / 대신 복제 사이즈가 작음
Row 기반 바이너리 로그 포맷: 

## 복제 방식
### Async
<img src="3.png" width="75%">

### Semi-Sync
<img src="4.png" width="75%">

### Sync
실제 relay 받고 replica 에서 commit 완료 까지 대기

## MSR (Multi Source Replication)
샤딩을 해서 source 가 여러개인 경우를 의미 (N개의 source -> 1개의 replica)
그런데 각각의 샤딩 source 에 각 replication 이 존재하는게 낫지않나?