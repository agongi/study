# Kafka Stream
```
https://kafka.apache.org/22/documentation/streams/quickstart
https://kimseunghyun76.tistory.com/464?category=757035
```

| 항목              | Kafka Streams                            | Kafka Connect                           |
|-------------------|------------------------------------------|------------------------------------------|
| 목적              | 실시간 데이터 처리 및 변환                | 외부 시스템과 Kafka 간 데이터 수집/전송 |
| 사용 방식         | Java 애플리케이션 내 `라이브러리`로 사용    | 독립 실행 커넥터로 실행 (`플러그인` 구조)  |
| 개발 필요 여부   | 코드 작성 필요 (Java 기반 스트림 처리)    | 설정만으로 사용 가능 (코드 불필요)       |
| 데이터 흐름       | Kafka → Streams App → Kafka               | 외부 시스템 ↔ Kafka                      |
| 내결함성 및 재시작| 애플리케이션 수준에서 관리 필요            | Connect 프레임워크에서 자동 관리         |
| 확장성           | 수동 확장 (앱 스케일 아웃 필요)           | 클러스터 기반 자동 확장                  |
| 상태 저장         | State Store 사용 (로컬 또는 RocksDB)      | 상태 저장 없음                           |
| 주요 사용 사례    | 이벤트 기반 처리, 실시간 변환, 집계       | RDB, S3, Elasticsearch 등과 연동         |
| 트랜잭션 지원     | Kafka 트랜잭션 일부 지원                  | 일부 커넥터만 트랜잭션 지원               |
| 복잡한 로직 처리  | 복잡한 로직 가능 (필터, 집계, 조인 등)     | 단순 데이터 흐름 중심 (ETL)              |

🎯 목적
- Kafka 내부에서 애플리케이션 레벨로 실시간 데이터 처리(stream processing) 를 하기 위한 라이브러리

🛠️ 특징
- `라이브러리 형태` (Spring Boot, Java 애플리케이션에 통합 가능)
- Kafka Consumer/Producer API 기반으로 동작
- 상태 저장이 가능 (stateful processing: window, aggregation, join 등)
- 로컬 상태 저장소(rocksdb) + Kafka changelog 토픽으로 복구 가능
- 분산 처리를 위해 Kafka의 파티션을 기반으로 scale-out
- fault-tolerant, exactly once 지원

✅ 주요 기능
- KStream, KTable, GlobalKTable 추상화
- windowing, joining, aggregating 등 stream 연산
- 실시간 ETL, 이벤트 처리, 모니터링 등에 적합

💻 코드 예시 (Java)
```java
StreamsBuilder builder = new StreamsBuilder();
KStream<String, String> stream = builder.stream("input-topic");
stream.mapValues(v -> v.toUpperCase())
      .to("output-topic");
```
