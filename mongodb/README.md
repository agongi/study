## MongoDB

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://docs.mongodb.com/manual
```

#### Index

- WriteConcern
- ReadConcern
- [How `_id` handled](how-id-handled)
- [Non-blocking secondary read](non-blocking-secondary-read)
- [Wildcard Index](wildcard-index)

#### Blog

- [Optimistic Locking](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongo-template.optimistic-locking)

### Overview

| RDBMS       | Mongo                                                   |
| ----------- | ------------------------------------------------------- |
| Database    | Database                                                |
| Primary key | Primary key (Default key _id provided if not specified) |
| Table       | Collection                                              |
| Tuple/Row   | Document                                                |
| Column      | Field                                                   |
| Table Join  | Embedded Documents                                      |

#### Collection

Table 과 동일한 개념

#### Document

Collection 안의 Document 는 서로 다른 field 를 가질 수 있습니다.

> 이러한 특징을 Dynamic shcema 라고 함

- Sample

```json
{
   _id: ObjectId(7df78ad8902c)
   title: 'MongoDB Overview',
   description: 'MongoDB is no sql database',
   by: 'tutorials point',
   url: 'http://www.tutorialspoint.com',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100,
   comments: [ 
      {
         user:'user1',
         message: 'My first comment',
         dateCreated: new Date(2011,1,20,2,15),
         like: 0
      },
      {
         user:'user2',
         message: 'My second comments',
         dateCreated: new Date(2011,1,25,7,45),
         like: 5
      }
   ]
}
```

#### Index

**Index Creation**

인덱스 생성은 아래 문법으로 가능하다:

```json
db.user.createIndex({name:1, age:1, createDate:1})
```

> 1: asc, -1: desc
>
> Collection 은 최대 64개의 Index 를 가질 수 있다.

생성 확인

```json
db.user.getIndexes()
```

**Index Prefix**

Compound Index 의 경우 partial match 에 대해 신경써야함

```json
db.user.createIndex({name:1, age:1, createDate:1})

- name age :: hit
- name :: hit

- age createDate :: nope
- createDate :: nope
- name createDate :: nope
```

**Sort**

생성된 Index 에 따른 정렬방향이 `매우` 중요하다:

> Sort 방향은 index pattern and/or inverse index pattern 과 일치해야한다.

```json
db.user.createIndex({name:1, age:1, createDate:1})

- db.user.find({}).sort({name: 1, age: 1}) :: hit
- db.user.find({}).sort({name: -1, age: -1}) :: hit

- db.user.find({}).sort({name: 1, age: -1}) :: nope
- db.user.find({}).sort({name: -1, age: 1}) :: nope
```

**Explain**

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

**Hint**

강제로 태울 인덱스를 지정가능

```json
db.user.find({}).hint({name: 1, age: 1})
```

