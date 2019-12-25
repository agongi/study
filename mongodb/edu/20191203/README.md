용어정리
dirty page (memory 와 실제 DB의 data 가 다른 그 diff)
checkpoint (메모리값와 DB 를 동기화해주는 행위)

Engine (wired tiger)

- == innoDB
- document-scope lock
- b-tree index

메모리

- 물리메모리의 절반 (50%) 사용 (ex. 128GB -> 64GB)
- document 별 가변 page
- (데이터) 원본저장, DB 에는 압축 (IO 사이즈가 줄겠지, 50% 정도)
- (인덱스) prefix 압축 (aaac, aaacd -> *, *d)

압축알고리즘
zlib 압축효율좋고, 느림
snappy 약간의효율, 빠른 (현재표준)
zstd (4.2 표준)

쓰기

- buffer write (memory)
- journal log write (disk, sequential)
- write done!
- (later) DB write (disk, random)
  : checkpoint 시점에 (60s) dirtyPage 싱크
  :: 그때시점에 write 가 많았다면, CPU 사용률 급증 (전체를 모두 다 함, 나눠서 하지 않음)

- skipList (== undo log)
- rw 를 OS 의 blockManager (cached IO) 위임 (알아서 커널레벨에서 잘하겠지)
  : postgresql 도 cachedIO 사용함
  : OS 에 부하에 따라 DB linear 성능에 영향을받음

읽기

- 버퍼에 있으면 바로 cache hit
- os 캐시에 (blockManager) 있으면 cache hit
- 80% 이상, 사용시 evict 시작 (LRU)
- 95% 이상, 강제 evict (아직 더티라면, LAS (sequential) 파일에 저장하며 삭제)
  : 추후 여유가 생기면 LAS 를 DB 에 반영

ReplicaSet

- oplog 를 생성하고, secondary 에서 그 파일을 반영
- read 는 부하분산 가능/write 는 primary 만 가능함

롤백

- primary 가 죽음
- oplog 마저 유실
- (다른 누군가가 primary 선출되고) 복구되어, secondary 로 재진입
- primary 에게 oplog 를 받아서, 싱크시작 (initial sync)
  : 최초투입이거나, 재시작시 init-sync 진행. 동기화이슈로 1-thread
- 받아보니 나만있던 데이터가 있네? (예전 primary 시절)
- 현재 oplog 기준으로 해당 데이터는 삭제하고, file 로 기록 (즉 기록은 남이있음)
  : 반영은 개발에서 판단

Replica (실시간 복제, based on oplog)

- (3.6) global write lock 을 걸고, 복제시작 (정합성 보장을 위해)
- (4.0)lock 없어진 대신, 스냅샷을 찍고 그 데이터로 secondary read 바로 지원
  : 20-30ms 차이로 찍음, 그정도의 gap 이 존재

샤딩

- getMore (100 document 요청시, N 개씩 짤라서 요청/응답함)
  : 몽고앞에 L4 를 두면 문제발생

샤드키 (중요함)

- 해시샤드
  : 1-1 로 해시값 생성 (meta 의 사이즈가 증가)
  : in 검색은 broadcast (N random-access)
- 레인지샤드
  : 샤드키를 range 로 잡아서 샤딩
  : in 검색은 타겟조회 (일 가능성이 높다. 동일 range 에 있다면)
- chunk 64MB
  : 샤드키를 기준으로 쪼개진 데이터 조각
- 제약조건
  -- 유니크인덱스
  : 각 샤드에서의 unique 만 보장, mongos 를 통해 aggregate 된 결과에는 N 가능하다. (로직방어필요)
  -- 조인
  : shard-join 안됨

인덱스

- 클러스터인덱스 미지원
- 16KB 사이즈 고정
- 최대 64개 설정가능 (collection-scope)
- primary(_id 고정)/secondary index 는 자유롭게
- 인덱스 생성시 DB lock (4.2 부터는 collection lock)
- wildcard 는 4.2 부터
- ttl index 는 1분단위로 돌면서 지움

Aggregation
aggregation 은 pipeline 으로 순차 a|b|c 로 수행됨. 즉 $match 로 맨처음에 필터링이 필수!
그리고 $projection 으로 field 를 필요한것만 가져오자

- 집합함수 (avg, min, max, count, sum 등)
- 샤드클러스터에서 모든 collection 이 샤딩되는건 아님 (각각 설정가능)
- $lookup
  : 조인 (== left join) 샤딩에선 조인할때 broadcast 나갈수있음
  : localField/foreignField 를 명확히 지정해서 1-N 조인 안되도록 지정하자 (1-1 되도록)
  : 미지정시 M-N (cross-join) 걸림
- $match
  : where, having
- $projection
  : select a, b, c 로 field projection
- $merge, $out
  : 임시테이블 만들고, 머지후 리턴. 샤드-primary (driving) 에 만드므로 특정샤드 부하 가중
- 몽고 쿼리는 (include aggregation) 메모리 100MB 이상 사용시, 무조건 exception
- aggregation 부하는 mongos 가 부담한다
  : broadcast 발생 쿼리는, 최종적으로 mongos 에서 정렬
  : 대신 allowDiskUse: true 하면 shard-replicaSet 의 primary 가 담당 (random)
  : $out, $lookup 가 포함되었으면 one-of-shard-primary 가 부하담당

Read conern

- local
  : 요청받은 내가 응답
  : 중복은 제거함 (shard, secondary 에서는 이거?쓰자)
- available
  : 샤딩에서 secondaryRead 일때 중복발생 (primary 는 미발생), chunk mig (분배) 시 중복발생가능
  : 샤드안에서는 유니크 보장하지만, 샤드-클러스터끼리의 중복은 언제나 존재할 수 있음 (고아)
  : primary 에서 읽을때는 중복제거함 (shard-secondary 에서만 발생) since 3.6
- majority
  : 과반수이상의 동일한 결과로 응답 (psa 구성에선 치명적)
  : PSA 일때 PS 중 1개가 죽으면, majority 보장이 안되므로 응답해야하는것을 skiplist 저장
  : 그 skiplist 가 메모리 100% 치면서 클러스터장애

청크마이그레이션 > 샤드불균형 일때, 데이터의 리밸런싱 (청크단위로 떼어서 다른샤드로 옮기는 과정)


Write concern

- w
  : 1 2 3 4 5 ... 메모리에 몇대까지
- majority
  : 과반이상 메모리
- j
  : with journal?
- casual consistency session
  : r/w majority 이상, casual consistency session ON 해야함

Lock

- document lock 까지 지원 (wiredTiger)
- sub-document CUD 라고해도, document-level lock 이므로 전체가 걸림
- shardlock rw x
- exclusivelock w x
- intentlock collectionlock 대신 document 는 못지우게
- yield
  : slow-query 라면, IO 작업동안 리소스양보 (CPU)
- 4.0 부터는 multi-docu ACID (replicaSet)/4.2부터는 shard 도 지원
  : client 에서 (서버와동일한 driver, tx 를 명시적으로 여는 코드사용) 이 병행되어야함
- 쓰기충돌 (writeConflicts) - CUD 가 document 에 몰렸을때
  : document 동시변경시 행위가 다름
  : write 를 계속 수행함 until timeout (rdb 는 block 되어서, 대기) -> 오버헤드

Consistency

- tx 는 클러스터 내부의 logicalTime(ms) 로 관리하면서, ms 가 다른것까지만 tx 보장됨
- getMore() 는 sse 처럼..

페이징쿼리

- skip/limit 로 페이징쿼리가 직접가면
  : 페이지사이즈 * shard.length 만큼 실제로 데이터가져와서
  : 정렬
  : 필터
- shardKey + 필터조건 + 정렬조건까지 compoundIndex 로 만들고
  : 인덱스내부에서 _id 를 찾을수있게 (index covered)
  : 그 후 페이징에 해당하는 _id 만 fetch 해서가져옴
  : 즉 mongos 에서 각 샤드의 index 를 가져오고, 그안에서 인덱스로 커버


트러블슈팅

- mongos -- mongod 커넥션 이슈
  : core * 10 개의 커넥션 맺음 (각 샤드의 node 마다 각각)

- 고아객체 (chunk mig) 에 따른.. shard-secondary 일때만 생김
  : 

16MB 가 1개의 document max size




[mongo]
readConcern - rdb 의 isolation_level 정도로 이해하면됨 (read_committed)
writeConcern - ...

readConcern - available 은
secondary read 일때 장애가능

arbitary 는 멤버가 아님 (투표만한다)

p-s-a 구성일때 readConcern, majority 는 장애

- 아비터는 멤버가아님
- majority 는 과반이상의 최신을 응답하는 정책
- p, s 장애시 과반을 항상 넘지못함
- 그래서 oplog 를 계속 남김 (secondary 에 반영되야할 데이터를 기록)
- 메모리 뻗어서죽음

> psa 구성에서는 read:majority OFF해야함

3.6이상에서 secondary read 라면, local or majority 해야함 (shard 몽고)

- shard 몽고에서 secondary read시, 중복 DOC 조회됨
- secondary 의 고아 doc 이 후처리되지않음, primary 는 제거하고 응답
- 고아가 생기지않을 readConcern 으로 secondary 에서 읽어야함

> shard 구성에서는, secondary read 시 local/majority
>
> (local/majority 중복제거, available 중복가능성)

write readConcern
w - 메모리 O, 저널 X
majority - 과반이상 메모리 O, 저널 X
j - 메모리 O, 저널 X