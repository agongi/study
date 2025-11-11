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
### 1. Retryable Write (MongoDB 4.4)
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

## Read
### 1. Orphan Documents
Sharded Cluster 환경에서 청크 마이그레이션시 발생합니다:
- 청크 마이그레이션 완료
- 실제 청크는 A 와 B 모두 존재
  - A -> B 마이그레이션 완료후 A 는 삭제예정
- 실제 Config Server 에는 마이그레이션 완료이므로, B 에 존재한다고 메타정보 업데이트
  - `이후 cleanup 이 진행되기 전까지 그 순간 A 에는 orphan document 존재`
  - `readConcern: available 사용`시 A 에서 데이터 조회가능 (config server 를 거치지 않음)
  - local|majority 를 사용하면 config server 를 통해 B 를 통해 조회하므로 이슈없음
- 그후 cleanup 을 통해 A 제거

## BSON
몽고는 내부적으로 BSON 으로 document 를 저장합니다
- binary format 이므로 사이즈가 작고
- 필드의 type 을 지정가능

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