# Optimizer
```
https://hyuuny.tistory.com/207
https://willseungh0.tistory.com/161
```

## [방식](/mysql/execution-plan)
- rule-based
- cost-based

기본적으로 cost-based (통계정보) 로 옵티마이저가 실행계획을 선택 합니다

## 풀 스캔
- table full scan
  - 테이블을 처음부터 끝까지 읽는 방식입니다
  - 연속된 데이터 페이지가 조회되면 -> 포그라운드 스레드는 백그라운드 스레드에 의해 조회된 버퍼를 읽으므로 처리가 빨라집니다
    - `Read Ahead`: 백그라운드 스레드가 N-개의 페이지 단위로 미리 조회후 버퍼에 올려두는 작업
- index full scan
  - 인덱스를 처음부터 끝까지 읽는 방식입니다
  - SELECT COUNT(*) FROM person; 처럼 인덱스 전체 조회로 결과가 가능 한 경우 사용 합니다 

## 병렬처리
아무런 WHERE 조건 없이 테이블 전체 건수를 가져오는 쿼리만 병렬로 처리 할 수 있습니다
```sql
SET SESSION innodb_parallel_read_threads=2;

SELECT COUNT(*) FROM person;
```

## ORDER BY/GROUP BY
- 인덱스 사용
  - `Extra: Using index;`: 드라이빙 테이블의 컬럼으로 인덱스가 있다면 사용
    - `having 은 GROUP BY 이후에 필터링 하므로 인덱스 미사용`
- 인덱스 미사용
  - 임시 테이블은 메모리에 정렬/그루핑을 시도하지만, 사이즈가 커지면 디스크를 사용합니다
    - (ORDER BY) `Extra: Using filesort;`: 정렬 조건이 드라이빙 테이블에 있을때 사용
    - (ORDER BY) `Extra: Using temporary; Using filesort;`: 정렬 조건이 조인된 테이블에 있을때 사용
    - (GROUP BY) `Extra: Using temporary;`: 그루핑 조건이 드라이빙 테이블에 있을때 사용

## DISTINCT
select 에서 사용된 distinct 는 `모든 컬럼을 unique` 하게 조회합니다

## [Join](/mysql/join)