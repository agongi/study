## Non-blocking Secondary Reads

```
@author: suktae.choi
- https://www.mongodb.com/blog/post/mongodb-40-nonblocking-secondary-reads
```

### Concept

- Write가 primary에 반영 되고 secondary 전체 전달까지 secondary는 read를 block해서 데이터의 무결성을 보장함
- 그래서 주기적으로 높은 `Global Lock` 이 발생하여 read 성능이 저하됨
- MongoDB 4.0 이상부터 timestamp와 consistent snapshot을 이용해서 이 이슈를 해결함

#### consistent snapshot

TBD

#### timestamp

TBD

