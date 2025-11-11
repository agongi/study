# Change Streams
```
https://www.mongodb.com/ko-kr/docs/drivers/java/sync/current/logging-monitoring/change-streams/
https://docs.spring.io/spring-data/mongodb/reference/mongodb/change-streams.html
```

The oplog (operations log) is `a special capped collection` that keeps a rolling record of all operations that modify the data stored in your databases.
- Primary 는 write 발생시 oplog 에 기록
- Secondary 는 주기적으로 oplog 를 가져와서 반영 (async)
  - 자신의 `local.oplog.rs` collection 에 복사본을 만듬
  - (default) size: 5% of disk/time: 24-hours

> node 가 투입되었을때, oplog 로그로 모든 변경을 따라갈 수 있다면 바로 init sync 과정을 피할수 있다.

## 1. 내부 동작 원리 (How it Works)
"단순히 API를 사용하는 것을 넘어, 내부 구조와 동작 방식을 이해하고 있는가? 이는 장애 대응 및 성능 최적화 능력과 직결된다."

답변 핵심:
Change Streams의 핵심에는 Oplog(Operations Log)가 있습니다.

1. Oplog의 역할:
    * Oplog는 MongoDB 복제(Replication)를 위해 모든 데이터 변경 작업을 기록하는 특별한 Capped Collection입니다.
    * Replica Set의 Secondary 멤버들은 Primary의 Oplog를 읽어 자신의 데이터를 동기화합니다.

2. Change Streams와 Oplog의 관계:
    * Change Streams는 바로 이 Oplog를 외부에 안전하게 노출하는 공식적인 API라고 할 수 있습니다.
    * `클라이언트가 collection.oplog.watch()를 호출하면, MongoDB는 Oplog를 tailing하는 커서(Tailable Cursor)를 생성하여 클라이언트에게 반환합니다.`
    * 이 커서를 통해 Oplog에 기록되는 새로운 변경 이벤트들이 실시간으로 클라이언트에게 스트리밍됩니다.

3. Resume Token의 중요성:
    * 모든 변경 이벤트에는 _id 필드에 "Resume Token"이 포함됩니다.
    * 이 토큰은 Oplog 내의 특정 시점(log sequence number)을 가리키는 북마크와 같습니다.
    * 네트워크 장애 등으로 클라이언트의 연결이 끊겼다가 재연결될 때, 마지막으로 수신한 Resume Token을 서버에 전달하면 중단된 시점부터 이벤트를 이어서 수신할
      수 있습니다. 이는 데이터 유실을 방지하는 매우 중요한 메커지즘입니다.

필수 제약 조건: Change Streams는 Oplog에 의존하므로, Replica Set 또는 Sharded Cluster 환경에서만 사용 가능합니다. (Standalone 인스턴스에서는 사용 불가)

## 2. 주요 기능 및 고급 활용법
"기본적인 사용법 외에, 실제 프로덕션 환경에서 유용하게 사용할 수 있는 고급 기능들을 알고 있는가?"

답변 핵심:

1. 서버 사이드 필터링 (Aggregation Pipeline):
    * watch() 메소드에 Aggregation Pipeline 배열을 전달하여 원하는 이벤트만 필터링할 수 있습니다.
    * 예시: status 필드가 'completed'로 변경되는 update 이벤트만 구독하고 싶을 때, $match 파이프라인을 사용하면 됩니다.
    * 장점: 클라이언트 측에서 모든 이벤트를 받아 필터링하는 것보다 훨씬 효율적이며, 네트워크 트래픽과 클라이언트 부하를 줄여줍니다.

2. 전체 문서 조회 (Full Document Lookup):
    * update 이벤트는 기본적으로 변경된 필드(updateDescription)만 포함합니다.
    * fullDocument: 'updateLookup' 옵션을 사용하면, 변경된 이후의 문서 전체를 이벤트에 포함시킬 수 있습니다.
    * `fullDocumentBeforeChange: 'whenAvailable' 옵션을 사용하면 변경 이전의 문서도 가져올 수 있습니다. (MongoDB 6.0 이상)`
    * 주의: 이 옵션은 변경 이벤트 발생 시 추가적인 디스크 조회(lookup)를 유발하므로 약간의 성능 저하가 발생할 수 있습니다.

3. 구독 범위 (Scope):
    * db.collection.watch(): 특정 컬렉션
    * db.watch(): 특정 데이터베이스 내의 모든 컬렉션
    * client.watch(): 클러스터 전체의 모든 변경 사항 (어드민 권한 필요)

## 3. 운영상 고려사항 및 Best Practices (가장 중요)
"실제 운영 환경에서 발생할 수 있는 문제점들을 예측하고, 이에 대한 해결책을 제시할 수 있는가? (시니어의 역량이 드러나는 부분)"

답변 핵심:

1. Oplog 사이즈 관리:
    * 문제점: Oplog는 Capped Collection이므로 크기가 고정되어 있습니다. 쓰기 작업이 매우 활발한 시스템에서 Change Stream 리스너가 장시간(e.g., 24시간)
      다운되면, 리스너가 가지고 있던 Resume Token이 가리키는 시점이 Oplog에서 이미 덮어쓰여 사라질 수 있습니다.
    * 결과: "Resume token is invalid" 오류가 발생하며, 스트림을 이어갈 수 없게 됩니다.
    * 해결책:
        * 모니터링: Oplog의 time-to-live (Oplog가 롤오버되기까지 걸리는 시간)를 주기적으로 모니터링해야 합니다.
        * Oplog 크기 조절: 시스템의 쓰기량과 장애 복구에 필요한 최대 시간을 고려하여 Oplog 사이즈를 충분히 크게 설정해야 합니다.
        * 예외 처리: Resume Token이 무효화되었을 때를 대비해, 애플리케이션 레벨에서 데이터를 재동기화(full-sync)하거나 특정 시점부터 다시 시작하는
          폴백(fallback) 로직을 반드시 구현해야 합니다.

2. Idempotency (멱등성) 보장:
    * Change Streams는 "At-least-once" 전송을 보장합니다. 즉, 네트워크 문제 등으로 인해 동일한 이벤트가 두 번 이상 전달될 수 있습니다.
    * 따라서 이벤트를 처리하는 소비자(Consumer)는 반드시 멱등성을 가지도록 설계해야 합니다. 동일한 이벤트를 여러 번 처리하더라도 결과가 항상 동일해야 합니다.

3. 권한 관리:
    * Change Streams를 사용하려면 최소한 read 권한과 changeStream 역할을 가진 유저가 필요합니다. 보안 원칙에 따라 최소한의 권한만 부여해야 합니다.

4. Error Handling:
    * 네트워크 오류, 권한 문제, Oplog 롤오버 등 다양한 예외 상황에 대한 처리 로직을 견고하게 작성해야 합니다. 단순히 for-await-loop만 사용하는 것이 아니라,
      try-catch 블록으로 감싸고 재시도 로직 및 로깅을 철저히 해야 합니다.