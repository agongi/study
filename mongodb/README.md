# MongoDB

```
@author: suktae.choi
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://docs.mongodb.com/manual
```

### Index
- [ObjectId](objectid)
- [Sharded Cluster](sharded-cluster)
- [ReplicaSet](replica-set)
- [CRUD](crud)
- [WriteConcern](write-concern)
- [ReadConcern](read-concern)
- [Transactions](transactions)
- [Index](index)
- [Query Plans](query-plans)
- [Aggregation](aggregation)
- [Orphan Documents](orphan-documents)
- [Change Streams](change-streams)
- [Non-blocking secondary read](non-blocking-secondary-read)
- [Causal Consistency](causal-consistency)
- [[19.12.03 ~ 12.05] 테크톡, 몽고DB](edu/20191203)

### Blog
- [Optimistic Locking](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongo-template.optimistic-locking)

### Versions
- MongoDB 4.2
- MongoDB 4.4
- MongoDB 5.0
- MongoDB 6.0

***

| RDBMS       | Mongo                                                   |
| ----------- |---------------------------------------------------------|
| Database    | Database                                                |
| Primary key | Primary key (Default key _id provided if not specified) |
| Table       | Collection                                              |
| Tuple/Row   | Document                                                |
| Column      | Field                                                   |
| Table Join  | Embedded Documents (or using $lookup to join)           |

## Collection
Table 과 동일한 개념

## Document
Row 와 동일한 개념

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

## Write
- write in buffer (memory, b-tree/raw)
- write in journal (disk, sequential)
- write done!
- (later) write in db (disk, random/compressed)
  - checkpoint (60s) 시점에 dirtyPage flush 수행

## Read
- read from buffer (memory)
- read from DB (disk) & store in buffer
  - cache is over than 80% of physical memory, eviction started
  - cache is over than 95% of physical memory, eviction started forcefully
  - if deleted memory is dirty, store in `*.LAS` file
  - LAS will be written in DB in future

## BSON

몽고는 내부적으로 BSON 으로 document 를 저장합니다.

- binary format 이므로 사이즈가 작고
- 필드의 type 을 지정가능

<img src="images/1.png" width="75%">

```json
{"hello": "world"} ->
\x16\x00\x00\x00           // total document size
\x02                       // 0x02 = type String
hello\x00                  // field name
\x06\x00\x00\x00world\x00  // field value
\x00                       // 0x00 = type EOO ('end of object')
 
{"BSON": ["awesome", 5.05, 1986]} ->
\x31\x00\x00\x00
 \x04BSON\x00
 \x26\x00\x00\x00
 \x02\x30\x00\x08\x00\x00\x00awesome\x00
 \x01\x31\x00\x33\x33\x33\x33\x33\x33\x14\x40
 \x10\x32\x00\xc2\x07\x00\x00
 \x00
 \x00
```