## How _id handled

```
@author: suktae.choi
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
```

String or BigInteger `_id` is converted into `ObjectId` by default.

- @Id annotated field
- Field name is id
- Default generated id inserted
- @MongoId is used to control over more specific case

