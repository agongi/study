# Kafka 4.0
```
https://kafka.apache.org/blog#apache_kafka_400_release_announcement
https://digitalbourgeois.tistory.com/949
```

## ZooKeeper 제거 & KRaft 기본 적용
기존 Kafka는 메타데이터 관리를 ZooKeeper에 의존했지만, Kafka 4.0부터는 KRaft(Kafka Raft) 모드가 기본으로 설정됩니다.

✅ KRaft의 장점
ZooKeeper 없이 Kafka 자체적으로 메타데이터를 관리 → 운영 부담 감소
컨트롤러 노드가 분산 환경에서 동적으로 조정됨 → 확장성 향상
불필요한 리더 선출을 줄여 더 빠른 장애 복구 가능

⚠️ 주의할 점
Kafka 4.0에서는 ZooKeeper 모드를 사용할 수 없음 → 3.9 버전에서 KRaft로 미리 전환해야 함
기존 ZooKeeper 기반 설정을 활용하려면 사전 마이그레이션 필요
