## MongoDB

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://docs.mongodb.com/manual
```

#### Index

- [CRUD](crud)
- [WriteConcern](write-conern)
- [ReadConcern](read-concern)
- [How `_id` handled](how-id-handled)
- [Non-blocking secondary read](non-blocking-secondary-read)
- [Wildcard Index](wildcard-index)
- [Atomicity and Transactions](atomicity-transactions)
- [Index](index)
- [ReplicaSet vs ShardCluster](replicaset-shardcluster)
- [Query Plans](query-plan)

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
