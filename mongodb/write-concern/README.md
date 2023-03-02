# Write Concern

```
@author: suktae.choi
- https://docs.mongodb.com/manual/reference/write-concern/
- https://www.mongodb.com/docs/manual/core/journaling
```

<img src="1.png" width="50%">

```json
db.collection.insert({...},
  {writeConcern: {
    w: 3,
    wtimeout: 150,
    j: false
  }
})
```

## w
The number of nodes that must be written before returning success

- 1 (default): primary only
- 2: primary + any of one secondary
- majority: members more than half

<img src="2.png" width="50%">

## j
write buffer 를 journal (disk) 까지 저장하는 것을 의미합니다 (== WAL)

> 즉 쓰기지연 하지 않고 disk 까지 저장

- true/false

해당 옵션이 없는 일반적인 write 는 `storage.journal.commitIntervalMs: (기본값) 100ms` 의 주기로 flush (to journal file) 된다

