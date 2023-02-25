# ObjectId

```
@author: suktae.choi
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://www.mongodb.com/docs/manual/reference/method/ObjectId/
```

## _id
spring-data-mongo 에서 id 로 사용하는 필드의 선택은 아래의 기준입니다.

- @Id annotated field
- Field name is id
- Default generated id inserted
- @MongoId is used to control over more specific case

## 구조
미지정시 기본으로 생성해주는 ObjectId 는 아래의 형태로 `오름차순 (timestamp) + 랜덤`을 보장합니다.

- 4-byte timestamp
- 5-byte random value
- 3-byte incrementer count
