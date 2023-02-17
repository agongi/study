# Aggregation

```
@author: suktae.choi
- https://docs.mongodb.com/manual/aggregation/
```

- 몽고 쿼리는 (include aggregation) 메모리 100MB 이상 사용시, 무조건 exception
- aggregation 부하는 mongos 가 부담한다
  - broadcast 발생 쿼리는, 최종적으로 mongos 에서 정렬
  - 대신 allowDiskUse: true 하면 shard-replicaSet 의 primary 가 담당 (random)
  - $out, $lookup 가 포함되었으면 one-of-shard-primary 가 부하담당


| SQL      | Aggregation          |
|----------|----------------------|
| select   | $project             |
| where    | $match               |
| group by | $group               |
| order by | $sort                |
| limit    | $limit               |
| count    | $sum -> $sortByCount |
| sum      | $sum                 |
| join     | $lookup              |
| flatten  | $unwind              |

- 일반적인 사용
```json
{
  "_id": "10280",
  "city": "NEW YORK",
  "state": "NY",
  "pop": 5574,
  "loc": [
    -74.016323,
    40.710537
  ]
}

db.zipcodes.aggregate([
  {$match: {totalPop: {$gte: 10 * 1000 * 1000}}}
  {$group: {_id: "$state", totalPop: {$sum: "$pop"}}},
])
```

- $unwind
```json
{
  _id : "jane",
  joined : ISODate("2011-03-02"),
  likes : ["golf", "racquetball"]
}

db.users.aggregate([
  {"$unwind": "$likes"},
  {"$group": {"_id": "$likes","number": {"$sum":1}}},
  {"$sort": {"number": -1}},
  {"$limit": 5}
])
```

```json
{
  _id : "jane",
  joined : ISODate("2011-03-02"),
  likes : "golf"
}
{
  _id : "jane",
  joined : ISODate("2011-03-02"),
  likes : "racquetball"
}
```