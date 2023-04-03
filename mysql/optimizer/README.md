# Optimizer

```
@author: suktae.choi
```

## 방식
- rule-based
- cost-based

## FULL SCAN
- table full scan
  - 테이블을 처음부터 끝까지 읽는 방식입니다
  - 연속된 페이지의 조회가 지속되면 -> (예측해서) N 개의 페이지 단위로 미리 읽고 버퍼에 올립니다 (== `Read Ahead`)
- index full scan
  - 인덱스를 처음부터 끝까지 읽는 방식입니다

## 병렬처리
병렬처리는 가능하지만 힌트를 따로 줄 순 없습니다.

> Oracle 과의 차이점

## ORDER BY
`Using filesort` 의 의미설명

- 인덱스를 이용한 정렬
- filesort를 이용한 정렬

## GROUP BY

## DISTINCT

## 내부 임시 테이블

## JOIN

## HINT