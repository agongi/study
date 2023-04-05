# Optimizer

```
@author: suktae.choi
```

## 방식
- rule-based
- cost-based

기본적으로 cose-based 로 옵티마이저가 동작합니다.

## FULL SCAN
- table full scan
  - 테이블을 처음부터 끝까지 읽는 방식입니다
  - 연속된 페이지의 조회가 지속되면 -> (예측해서) N 개의 페이지 단위로 미리 읽고 버퍼에 올립니다 (== `Read Ahead`)
- index full scan
  - 인덱스를 처음부터 끝까지 읽는 방식입니다

## 병렬처리
병렬처리는 가능하지만 힌트를 따로 줄 순 없습니다.

> Oracle 은 지원함

## ORDER BY (Using filesort;)
실행계획에서 `Using filesort` 는 인덱스를 이용한 정렬을 하지 못했다는 의미입니다.

### 알고리즘
- (1번) 레코드 전체 (조회발생) 를 sort buffer 에서 처리
- (2번) 정렬에 필요한 field (조회발생) 를 sort buffer 에서 처리후, 같이 조회한 P.K 로 레코드 재조회

기본적으로 레코드 전체를 1회 조회후 소트버퍼에서 정렬하는 방식이 플랜으로 잡힙니다.

대신 각자의 장/단점이 명확하므로 처리되는 데이터의 사이즈에 따라 선택됩니다
- 조회되는 레코드가 많은경우: 2번방식
  - 소트버퍼 OOM 방지위함
- 레코드가 적은경우: 1번방식
  - 조회를 줄이기 위함

### 처리 방법
- 인덱스 정렬
  - Extra: 별도 표기 없음
- (JOIN) 드라이빙 테이블 정렬
  - Extra: Using filesort;
- (JOIN) 결과를 임시테이블에 저장후 정렬
  - Extra: Using temporary; Using filesort;

## GROUP BY
TBD

## DISTINCT
select 에서 사용된 distinct 는 `모든 컬럼을 unique` 하게 조회합니다

## 내부 임시 테이블
`Using temporary;` 를 통해 생성되는 임시 테이블은 메모리에 생성되었다가 테이블의 사이즈가 커지면 디스크로 옮겨집니다.

## [Join](/mysql/join)

## HINT