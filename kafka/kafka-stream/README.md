# Kafka Stream
```
https://kafka.apache.org/22/documentation/streams/quickstart
https://kimseunghyun76.tistory.com/464?category=757035
```

| 항목              | Kafka Streams                  | Kafka Connect                |
|-------------------|--------------------------------|------------------------------|
| 목적              | 카프카를 통한 데이터 실시간 처리 목적          | 카프카를 통한 데이터 전송/연동 목적         |
| 사용 방식         | Java 애플리케이션 `라이브러리`            | 독립된 java agent 실행 `플러그인`     |
| 개발 필요 여부   | 코드 작성 필요 (Java 기반 스트림 처리)      | 설정만으로 사용 가능 (코드 불필요)         |
| 데이터 흐름       | Kafka → 스트리밍 (가공) → Kafka      | 외부 → Kafka → 외부              |
| 내결함성 및 재시작| 애플리케이션 수준에서 관리 필요              | Connect 프레임워크에서 자동 관리        |
| 주요 사용 사례    | 이벤트 기반 처리, 실시간 변환, 집계          | RDB, S3, Elasticsearch 등과 연동 |
| 복잡한 로직 처리  | 복잡한 로직 가능 (필터, 집계, 조인 등)       | 단순 데이터 흐름 중심 (ETL)           |

🎯 목적
- Kafka 내부에서 애플리케이션 레벨로 실시간 데이터 처리(stream processing) 를 하기 위한 라이브러리

🛠️ 특징
- `라이브러리 형태` (Spring Boot, Java 애플리케이션에 통합 가능)
- Kafka Consumer/Producer API 기반으로 동작
- 상태 저장이 가능 (stateful processing: window, aggregation, join 등)
- `로컬 상태 저장소(rocksdb)` + Kafka 토픽으로 복구 가능
  - KTable 은 토픽의 모든 데이터를 Materiazlied View 생성 (RocksDB 에 저장)
  - 이후 실시간 데이터처리는 KTable 기반으로 동작 (불필요한 카프카 질의 없음)
- 분산 처리를 위해 Kafka의 파티션을 기반으로 scale-out

✅ 주요 기능
- KStream, KTable, GlobalKTable 추상화
- windowing, joining, aggregating 등 stream 연산
- 실시간 ETL, 이벤트 처리, 모니터링 등에 적합

💻 코드 예시 (Java)
```java
@Bean
public KStream<String, PageViewEvent> pvStream(StreamsBuilder builder) {
    var source = builder.stream("pv-events",
        Consumed.with(Serdes.String(), JsonSerdes.of(PageViewEvent.class))
    );

    var aggregated = source
        .groupByKey() // 카프카키 기준으로
        .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(1)))   // 최근 1분동안
        .aggregate(
            () -> 0L,
            (key, event, agg) -> agg + event.getPageViewCount(),  // 누적 카운트
            Materialized.with(Serdes.String(), Serdes.Long())
        );

    aggregated
        .toStream()
        .filter((windowedKey, totalCount) -> totalCount >= THRESHOLD) // 특정 THRESHOLD 이상이라면
        .map((windowedKey, totalCount) -> KeyValue.pair(windowedKey.key(), totalCount))
        .to("hot-products", Produced.with(Serdes.String(), Serdes.Long())); // 별도 가공된 값을 신규토픽으로 전파

    return source;
}
```
