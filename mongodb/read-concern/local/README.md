### Local

```
ㅁ Author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern-local/
```

Read 요청이 도달한 그 node 에 있는 data 를 읽는다.

대신 바로 응답하지 않고, shard-primary and/or config server 에 질의해서 고아객체 등을 제거함이 보장된다.