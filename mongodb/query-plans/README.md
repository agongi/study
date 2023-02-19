# Query Plans

```
@author: suktae.choi
- https://www.mongodb.com/docs/manual/core/query-plans/
```

101 번째 winning plan 이 실행계획으로 선정된다.
한번 캐싱된 실행계획은, mongod 재시작/인덱스 CUD 가 발생하지 않는한 유지된다.

## Explains
검색이 Index 를 타는지는 실행계획을 보면된다: [Explain Results](https://docs.mongodb.com/manual/reference/explain-results/)

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
			"direction" : "forward",/Users/user/com/app/gw/gw
/Users/user/Documents/Untitled.md
/Users/user/Documents/Untitled-1.md
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

자꾸 엉뚱한 인덱스를 탄다면 실행계획이 어떻게 판정되는지 살펴보자:

- 캐싱된 Execution plan 조회
  - 매칭 -> 해당 plan 사용
- 캐싱된 결과가 없다면, 모든 플랜으로 racing
  - `첫 101` 를 가장 빠르게 가져오는 결과를 winningPlan 으로 설정
  - 동점이라면, in-memory-sort 가 발생하지 않는 플랜을 선택
    - **32MB 를 넘는 결과는 memory-sort 불가능**
- 실행계획 캐시 저장
- (이후 동일 쿼리형태일 경우) 캐싱된 결과로 플랜사용
  - 캐시는 mongod 재시작 | 인덱스 변경시 evict

```
## 동일한 쿼리지만 플랜이 다르게 판단 할 수 있는 케이스
db.user.find({});
-- 해당 쿼리수행시, key-examined: 878976 정도이고, 첫 101 응답이 늦음

db.user.find({}).limit(101);
-- 제한을 걸어버리면, key-examined: 250 정도에서, 첫 101 응답

이런케이스가 나온다면 엉뚱한 인덱스가 winning 할 수 있음
```

## Hint
강제로 태울 인덱스를 지정

```json
db.user.find({}).hint({name: 1, age: 1})
```
