## Aggregation

```
ㅁ Author: suktae.choi
ㅁ References:
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

### Tips

- $match 를 먼저 사용하여, aggregation 되는 document 를 filtering 

> $match 조건에 index 필요

- $lookup 의 경우 조인되는 collection 과 localfield , forenfield 설정 필요

> Foreign field 에도 index 필요

- $sort 에도 index 필요

- 필요한 data 만 $projection 으로 제한