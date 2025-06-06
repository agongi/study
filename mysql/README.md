# MySQL
```
https://dev.mysql.com/doc/refman/8.0/en/
https://velog.io/@kmw89891/%EC%95%84%ED%82%A4%ED%85%8D%EC%B2%98
```

### Index
- [Optimizer](optimizer)
- [Execution Plan](execution-plan)
- [Isolation](isolation)
- [Locks](locks)
- [Index](index)
- [Join](join)
- [Replication](replication)
- [Prepared statement](prepared-statement)
- [MVCC](mvcc)
- [테이블 복제](insert-into-select)
- [중복 레코드 관리](duplicated-record)

### Blog
- [https://use-the-index-luke.com/sql/preface](https://use-the-index-luke.com/sql/preface)
- [Index Dive 비용 최적화](https://medium.com/daangn/index-dive-%EB%B9%84%EC%9A%A9-%EC%B5%9C%EC%A0%81%ED%99%94-1a50478f7df8)
- [EXISTS vs IN](https://velog.io/@emawlrdl/Oracle-IN-vs-EXISTS)
- [late row lookups](https://explainextended.com/2009/10/23/mysql-order-by-limit-performance-late-row-lookups/)

***
<img src='1.png' width="50%">

- MySQL
  - 커넥션 관리 (foreground thread)
  - 쿼리 실행 (파싱/전처리/옵티마이저 -> 실행계획 작성)
- Storage (InnoDB)
  - MySQL 엔진의 명령에 의해 실제 데이터의 read/write 담당

## MySQL 엔진
### Thread 구조
<img src='2.png' width="50%">

- foreground
  - 실제 client 요청을 받는 thread
- background
  - !foreground
  - log, buffer to disk(write), disk to buffer(read) ...

## InnoDB 스토리지 엔진
<img src='5.png' width="50%">

record 기반 잠금을 제공합니다

## Buffer
### Read Buffer
<img src='6.png' width="50%">

Buffer 를 이용한 조회는 아래의 흐름으로 처리됩니다

- 버퍼검색
  - hash index 검색
  - B+Tree index 검색
  - 조회되었다면 MRU 방향으로 승급
- 디스크 조회후 LRU 에 추가
  - bulk 조회의 경우 Buffer 에는 올라가지만, MRU 로 승격은 되지않음
- 자주 조회 된다면, Hash index 에 추가

### Write Buffer
변경된 데이터는 buffer 와 redo log 에 저장됩니다 (Write-Ahead Log == WAL)

- buffer 에 저장된 데이터는 조회시 수정된 내용을 응답하기 위해 저장되고
- `Redo 는 복원을 위함` 입니다

### Change buffer
인덱스의 변경이 발생했을때 (DML 쿼리)

- Buffer 에 인덱스가 있다면: 버퍼에 즉시반영
- 없다면: Change Buffer 에 기록 (쓰기지연) -> 이후 조회를 통해 page 가 버퍼에 저장된다면 change buffer 내용이 index 에 포함됨

대신 DISK 에 즉시반영 하지 않으므로 P.K or unique index 에는 change buffer 가 적용되지 않습니다

## Collation
`utf8mb4_0900_ai_ci` 기본값을 사용합니다

- utf8mb4
  - 4byte 문자셋 (emoji 지원)
- 0900
  - unicode 9.0
- ai
  - accent insensitive (대소문자 구분 없음)
- ci
  - case insensitive (대소문자 구분 없음)

## DataType
### 문자
| 타입        | 용량 제한                                | 길이    | 저장 방식             | 사용 용도                      |
|-------------|--------------------------------------|-------|-------------------|-------------------------------|
| `CHAR(n)`   | 최대 255 **바이트**                       | 고정 길이 | 공백 패딩하여 저장        | 짧고 정해진 길이 문자열        |
| `VARCHAR(n)`| 최대 65,535 **바이트** (전체 row 제한 포함)     | 가변 길이 | 길이 정보 + 실제 문자열 저장 | 대부분의 가변 문자열 데이터   |
| `TEXT`      | 최대 4GB (== LONGTEXT) | 가변 길이 | 별도 LOB 저장         | 대용량 텍스트 (예: 본문, 설명) |

### 바이너리
| 타입          | 용량 제한                                | 길이    | 저장 방식                     | 사용 용도                     |
|---------------|--------------------------------------|-------|------------------------------|------------------------------|
| `BINARY(n)`   | 최대 255 **바이트**                       | 고정 길이 | 공백(0x00) 패딩하여 저장       | 고정 크기 바이너리 데이터     |
| `VARBINARY(n)`| 최대 65,535 **바이트** (전체 row 제한 포함)     | 가변 길이 | 길이 정보 + 데이터 저장       | 파일 이름, 해시 값 등         |
| `BLOB`        | 최대 4GB (== LONGBLOB) | 가변 길이 | 별도 LOB 저장            | 이미지, 동영상, 파일 바이너리 |
