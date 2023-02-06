## Aggregation

```
@author: suktae.choi
- https://docs.mongodb.com/manual/aggregation/
```

### Cores

| SQL      | Aggregation           |
| -------- | --------------------- |
| select   | $project              |
| where    | $match                |
| group by | $group                |
| order by | $sort                 |
| limit    | $limit                |
| count    | \$sum -> $sortByCount |
| sum      | $sum                  |
| join     | $lookup               |

aggregation 은 pipeline 으로 순차 a|b|c 로 수행됨. 즉 $match 로 맨처음에 필터링이 필수!

그리고 $projection 으로 field 를 필요한것만 가져오자.

- 몽고 쿼리는 (include aggregation) 메모리 100MB 이상 사용시, 무조건 exception
- aggregation 부하는 mongos 가 부담한다
  - broadcast 발생 쿼리는, 최종적으로 mongos 에서 정렬
  - 대신 allowDiskUse: true 하면 shard-replicaSet 의 primary 가 담당 (random)
  - \$out, \$lookup 가 포함되었으면 one-of-shard-primary 가 부하담당

### Tips

- $match 를 먼저 사용하여, aggregation 되는 document 를 filtering 

> $match 조건에 index 필요

- $lookup 의 경우 조인되는 collection 과 localfield , forenfield 설정 필요

> Foreign field 에도 index 필요

- $sort 에도 index 필요

- 필요한 data 만 $projection 으로 제한

