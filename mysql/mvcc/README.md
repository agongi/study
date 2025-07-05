# MVCC (Multi Version Concurrency Control)
```
https://mangkyu.tistory.com/288
https://amaran-th.github.io/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4/[MySQL]%20%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98%20%EA%B2%A9%EB%A6%AC%EC%88%98%EC%A4%80%EA%B3%BC%20MVCC/
```

MVCC는 하나의 레코드의 여러 버전을 유지하여, `락 없이` 동시성을 높이는 방법입니다.

- `변경전` 데이터: Undo Log 에 저장
- `변경될` 데이터: Buffer Pool 에 저장

## Undo Log (== 버전 관리)
```sql
UPDATE member SET area = "경기" WHERE member_id
```

해당 쿼리의 동작을 정리하면 아래와 같습니다:
- 커밋 여부와 상관없이 버퍼풀 (메모리) 내용 변경
  - Undo Log 에는 변경 전 값 저장
- (다른 트랜잭션) SELECT 시 Undo Log 에 저장된 `N-개의 버전에서 자신의 TxID 보다 낮은` 데이터 조회
  -  트랜잭션 ID는 순차적으로 증가하며, 나중에 시작된 트랜잭션은 더 큰 ID를 가집니다
- 트랙잭션 종료
  - Commit: 현재 상태 유지 (춧후 버퍼풀의 내용은 디스크에 저장)
  - Rollback: Undo Log 에 저장된 값으로 복구

> Undo 에 저장된 데이터는 필요로 하는 트랜잭션이 없을 때까지 유지후 삭제

<img src="1.png" width="50%">

## Redo Log (== 복원)
데이터 변경 내용을 Redo Log 에 기록해서, 비정상 종료시 데이터 복구에 사용 합니다.
- Commit 후 디스크에 기록되지 않은 경우, Redo Log 를 통해 복구
  - Redo Log 자체도 버퍼에 저장하고, I/O 를 줄이기 위해 주기적으로 디스크 동기화
 
## 잠금 없는 일관된 읽기 (Non-Locking Consistent Read)
`현재 record lock 이 잡혀있더라도` Undo 를 통해 `대기없이` 과거 버전의 데이터를 Select 할수 있습니다 (Serializable 격리레벨이 아닌경우)

다만 `SELECT ... FOR UPDATE 나 SELECT ... FOR SHARE` 는 MVCC 를 사용하지 않고 실제로 잠금 (Shared/Exclusive Lock) 을 획득하여 현재 버전의 데이터만 조회합니다