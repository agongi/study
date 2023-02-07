## Index

```
@author: suktae.choi
- https://docs.mongodb.com/manual/indexes/
- https://docs.mongodb.com/manual/core/index-wildcard/
- https://www.mongodb.com/blog/post/coming-in-mongodb-42--1-wildcard-indexes
- https://blog.ull.im/engineering/2019/06/19/wildcard-indexes.html
```

### Cores

인덱스는 아래의 특징이 있다:

- `_id` 는 무조건 p.k, 나머지는 s.k 로 고정이다
- Collection 은 `최대 64개`의 인덱스 가능
- 1개의 인덱스는 `16KB 사이즈` 고정
- TTL 인덱스는 (TimeUnit.MINUTES, 1) 로 수행

Since 4.2:

- \* (Wildcard) index 지원
- 인덱스 생성시 DB lock -> Collection lock

### CRUD

인덱스 생성은 아래 문법으로 가능하다:

```json
db.user.createIndex({name:1, age:1, createDate:1})
```

> 1: asc, -1: desc

생성 확인

```json
db.user.getIndexes()
```

### Index Prefix

Compound Index 의 경우 partial match 에 대해 신경써야함

```json
db.user.createIndex({name:1, age:1, createDate:1})

- name age :: hit
- name :: hit

- age createDate :: nope
- createDate :: nope
- name createDate :: nope
```

### Multikey Index

CUD performance reduced based on the size of ratings array

```json
db.user.insert{ _id: 1, item: "ABC", ratings: [2, 9]}

db.user.createIndex({ratings: 1})
```

### Multikey + SubDocument

```json
db.user.insert{ _id: 1, item: "ABC", ratings: [
  {name: "XXX", score: 100},
  {name: "YYY", score: 99}]}

db.user.createIndex({ratings.score: 1})
```

### Unique Index

중복없는 Index 의 보장이 필요할떄 지정.

대신 shared-cluster 에서는 샤드키가 선행되어 포함되어야 생성가능

> 샤드환경에서는 동일샤드에서의 유니크만 보장 가능하기 때문 (ex. 고아객체)

### Partial Index

Nullable 한 field 의 인덱싱 YN

### Sort

생성된 Index 에 따른 정렬방향이 `매우` 중요하다:

> Sort 방향은 index pattern and/or inverse index pattern 과 일치해야한다.

```json
db.user.createIndex({name:1, age:1, createDate:1})

- db.user.find({}).sort({name: 1, age: 1}) :: hit
- db.user.find({}).sort({name: -1, age: -1}) :: hit

- db.user.find({}).sort({name: 1, age: -1}) :: nope
- db.user.find({}).sort({name: -1, age: 1}) :: nope
```

### Explain

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

자꾸 엉뚱한 인덱스를 탄다면 실행계획이 어떻게 판정되는지 살펴보자:

- 캐싱된 Execution plan 조회
  - 매칭 -> 해당 plan 사용
- 캐싱된 결과가 없다면, 모든 플랜으로 racing
  - `첫 101` 를 가장 빠르게 가져오는 결과를 winningPlan 으로 설정
  - 동점이라면, in-memory-sort 가 발생하지 않는 플랜을 선택
    - **32MB 를 넘는 결과는 memory-sort 불가능**
  - 결과저장
- 캐싱된 결과를 재사용
  - 실행계획 재수행은 특정조건에 따라 수행됨 (좀더 찾아보자)

```json
## 동일한 쿼리지만 플랜이 다르게 판단 할 수 있는 케이스
db.user.find({});
-- 해당 쿼리수행시, key-examined: 878976 정도이고, 첫 101 응답이 늦음

db.user.find({}).limit(101);
-- 제한을 걸어버리면, key-examined: 250 정도에서, 첫 101 응답

이런케이스가 나온다면 엉뚱한 인덱스가 winning 할 수 있음
```

> 엉뚱한 인덱스를 타지 못하게 원천적으로 꼭 필요한 Index 만 지정필요

### Hint

강제로 태울 인덱스를 지정가능

```json
db.user.find({}).hint({name: 1, age: 1})
```
