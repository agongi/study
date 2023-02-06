## MongoDB

```
@author: suktae.choi
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://docs.mongodb.com/manual
```

#### Index

- [CRUD](crud)
- [WriteConcern](write-concern)
- [ReadConcern](read-concern)
- [Transactions](transactions)
- [Index](index)
- [Aggregation](aggregation)
- ChangeStream
- [Sharded Cluster](sharded-cluster)
- [ReplicaSet](replica-set)

#### Extras

- [How `_id` handled](how-id-handled)
- [Non-blocking secondary read](non-blocking-secondary-read)
- [[19.12.03 ~ 12.05] 테크톡, 몽고DB](edu/20191203)

#### Blog

- [Optimistic Locking](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongo-template.optimistic-locking)

***

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

Row 와 동일한 개념

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

#### Write

- write in buffer (memory, b-tree/raw)
- write in journal (disk, sequential)
- write done!
- (later) write in db (disk, random/compressed)
  - checkpoint (60s) 시점에 dirtyPage flush 수행

#### Read

- read from buffer (memory)
- read from DB (disk) & store in buffer
  - cache is over than 80% of physical memory, eviction started
  - cache is over than 95% of physical memory, eviction started forcefully
  - if deleted memory is dirty, store in `*.LAS` file
  - LAS will be written in DB in future