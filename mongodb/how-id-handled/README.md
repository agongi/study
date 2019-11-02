## How _id handled

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://docs.mongodb.com/manual
```

String or BigInteger `_id` is converted into `ObjectId` by default.

- @Id annotated field
- Field name is id
- Default generated id inserted
- @MongoId is used to control over more specific case

