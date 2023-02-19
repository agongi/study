# Query Plans

```
@author: suktae.choi
- https://www.mongodb.com/docs/manual/core/query-plans/
```

검색이 Index 를 타는지는 https://docs.mongodb.com/manual/reference/explain-results/을 확인한다.

```json
db.user.find({}).explain();

{
	"queryPlanner" : {
		"plannerVersion" : 1,
		"namespace" : "test.mycollection",
		"indexFilterSet" : false,
		"parsedQuery" : {

		},
		"winningPlan" : {
			"stage" : "COLLSCAN",
			"direction" : "forward"
		},
		"rejectedPlans" : [ ]
	},
	"executionStats" : {
		"executionSuccess" : true,
		"nReturned" : 2,
		"executionTimeMillis" : 4,
		"totalKeysExamined" : 0,
		"totalDocsExamined" : 2,
		"executionStages" : {
			"stage" : "COLLSCAN",
			"nReturned" : 2,
			"executionTimeMillisEstimate" : 0,
			"works" : 4,
			"advanced" : 2,
			"needTime" : 1,
			"needYield" : 0,
			"saveState" : 0,
			"restoreState" : 0,
			"isEOF" : 1,
			"invalidates" : 0,
			"direction" : "forward",
			"docsExamined" : 2
		}
	},
	"serverInfo" : {
		"host" : "Reidui-MacBookPro.local",
		"port" : 27017,
		"version" : "4.0.4",
		"gitVersion" : "f288a3bdf201007f3693c58e140056adf8b04839"
	},
	"ok" : 1
}
```

- 캐싱된 Execution plan 조회
  - 매칭 -> 해당 plan 사용
- 캐싱된 결과가 없다면, 모든 플랜으로 racing
  - `첫 101` 를 가장 빠르게 가져오는 결과를 winning plan 으로 설정
  - 동점이라면, in-memory-sort 가 발생하지 않는 플랜을 선택
    - **32MB 를 넘는 결과는 memory-sort 불가능**
- 실행계획 캐시 저장
- (이후 동일 쿼리형태일 경우) 캐싱된 결과로 플랜사용
  - mongo 재시작 or collection drop | index CUD 가 발생하면 플랜캐시는 초기화 된다

## 잘못된 실행계획이 winnind plan 으로 판정되는 경우
```json
db.user.find({});
-- 해당 쿼리수행시, key-examined: 878976 정도이고, 첫 101 응답이 늦음

db.user.find({}).limit(101);
-- 제한을 걸어버리면, key-examined: 250 정도에서, 첫 101 응답
```

이런케이스가 나온다면 엉뚱한 인덱스가 winning 할 수 있음

## Hint
RDB 와 동일하게 인덱스 지정이 가능하다.

```json
db.user.find({}).hint({name: 1, age: 1})
```
