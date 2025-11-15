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

***
### 1. 개요: 왜 Zookeeper를 제거했는가?
Kafka가 Zookeeper를 제거한 이유는 단순한 의존성 제거가 아니라 기존 아키텍처의 구조적 한계를 해결하기 위해서입니다.

기존 구조의 문제점
- 운영 복잡성 증가
  - Kafka 브로커 + Zookeeper 앙상블을 각각 설치, 모니터링, 튜닝해야 함.
- 아키텍처 이질성
  - 서로 다른 데이터 모델·프로토콜로 인해 메타데이터 동기화가 비효율적.
- 확장성 한계
  - 모든 메타데이터 변경이 Zookeeper 경유 → 대규모 토픽/파티션 환경에서 병목 발생.
  - Controller failover 시간이 매우 길어지는 문제 존재.

### 2. 핵심 아키텍처 변경: KRaft(Kafka Raft) 도입
Kafka는 Zookeeper를 대체하기 위해 자체 합의 프로토콜인 KRaft를 도입했습니다.

### 2.1 컨트롤러 쿼럼 (Controller Quorum)
- Kafka 브로커 일부가 process.roles=controller 로 동작.
- 컨트롤러들이 Raft 합의를 수행하며 클러스터 메타데이터를 관리.

### 2.2 이벤트 소싱 기반 메타데이터 저장
- 기존: Zookeeper ZNode에 메타데이터 저장
- 변경: __cluster_metadata 내부 토픽에 이벤트 로그로 저장

### 2.3 로컬 상태 저장
- 각 컨트롤러는 메타데이터 로그를 로컬 디스크에도 유지.
- 이벤트 replay로 빠르게 상태 복구 가능.

### 2.4 빠른 리더 선출(Failover)
- Zookeeper 기반의 “전체 상태 재조회” 과정 제거
→ failover 시간이 수십 초 → 밀리초로 단축.

### 3. 배포 모델: Combined vs Isolated
KRaft 모드는 두 가지 배포 모델을 제공합니다.

### 3.1 Combined Mode (통합 모드)
```
process.roles=broker,controller
```
- 장점: 설정 간단, 서버 수 적음 → 개발/테스트 환경 적합
- 단점: 브로커와 컨트롤러 리소스 경합

### 3.2 Isolated Mode (분리 모드)
```
process.roles=controller
process.roles=broker
```
- 장점: 컨트롤러 독립 → 고가용성, 대규모 운영 환경 권장
- 단점: 물리 서버 수 증가