# Kafka Stream
```
https://kafka.apache.org/22/documentation/streams/quickstart
https://kimseunghyun76.tistory.com/464?category=757035
```

🧩 Kafka Streams
🎯 목적
- Kafka 내부에서 애플리케이션 레벨로 실시간 데이터 처리(stream processing) 를 하기 위한 라이브러리.

🛠️ 특징
- 라이브러리 형태 (Spring Boot, Java 애플리케이션에 통합 가능).
- Kafka Consumer/Producer API 기반으로 동작.
- 상태 저장이 가능 (stateful processing: window, aggregation, join 등).
- 로컬 상태 저장소(rocksdb) + Kafka changelog 토픽으로 복구 가능.
- 분산 처리를 위해 Kafka의 파티션을 기반으로 scale-out.
- fault-tolerant,-  exactly-once semantics 지원.

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