## MongoDB

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring-data/mongodb/docs/current/reference/html
- https://docs.mongodb.com/manual
```

#### Index

- CRUD
- WriteConcern
- ReadConcern

### Cores

#### How `_id` handled?

String or BigInteger `_id` is converted into `ObjectId` by default.

- @Id annotated field
- Field name is id
- Default generated id inserted
- @MongoId is used to control over more specific case

#### Field Encryption

```java
MongoJsonSchema schema = MongoJsonSchema.builder()
  .properties(
  encrypted(string("ssn"))
  .algorithm("AEAD_AES_256_CBC_HMAC_SHA_512-Deterministic")
  .keyId("*key0_id")
).build();
```

> Supported since 4.2

#### Blog

- [Optimistic Locking](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongo-template.optimistic-locking)