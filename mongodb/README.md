# MongoDB
```
https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
https://docs.mongodb.com/manual
```

### Blog
- [Optimistic Locking](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongo-template.optimistic-locking)

***
| RDBMS       | Mongo                                                   |
| ----------- |---------------------------------------------------------|
| Database    | Database                                                |
| Primary key | Primary key (Default key _id provided if not specified) |
| Table       | Collection                                              |
| Tuple/Row   | Document                                                |
| Column      | Field                                                   |
| Table Join  | Embedded Documents (or using $lookup to join)           |

## Lock
TBD

## Write
### 1. Retryable Write
Retryable writes allow MongoDB drivers to automatically retry certain write operations `a single time` in driver level regardless of whether retryWrites option is set to **false.**

> Operations in transactions are not individually retryable but `whole are` on it.

### 2. Multi-Document
1개의 document 를 수정하는 명령어만 bulk 처리가 가능합니다. (updateMany 처럼 multi-document 는 bulk operation 미지원)

| Methods     | Descriptions      |
| ----------- | ----------------- |
| #insertOne  | Supported         |
| #updateOne  | Supported         |
| #deleteOne  | Supported         |
| #replaceOne | Supported         |
| #updateMany | **Not supported** |

## BSON
몽고는 내부적으로 BSON 으로 document 를 저장합니다
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