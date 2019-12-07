### ShardKey

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.mongodb.com/manual/core/sharding-shard-key/
```

### Hashed Sharding

ShardKey 를 Hash 해서 샤딩하는 방식이다.

샤드키의 갯수만큼 해시의 테이블이 관리된다.

> 즉 데이터가 많아지면 Config 서버의 데이터도 linear 하게 증가하므로, 성능이 같이 느려진다.

### Ranged Sharding

해시를 하지않고, 범위로 짤라서 샤딩한다.

즉 샤드키가 close 한 document 일수록 동일 샤드에 있는 확률이 높다.

대신 부하가 특정샤드에만 집중될 수 있다. (분산되지 않음)

- linear 하게 증가하는 값이 샤드키라면, 일정범위까지 동일 샤드 (청크) 로 몰릴것이다.
- 즉 write 가 특정샤드로만 집중된다.

