## Locks
This section describes lock types used by InnoDB.

> Those lock mechanism is tradeoff performance and concurrency.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.07.29
ㅁ References:
 - https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html
 - http://ktdsoss.tistory.com/382
```

### 1. Record Locks

A record lock is a lock on an index record literally.

```
update record [2]

[1]--------[2]---------[3]---------[4]
      record-lock
```

### 2. Gap Locks

A gap lock is a lock on a gap between index records, or a lock on the gap before the first or after the last index record.

```
update record [2]

[1]--------[2]---------[3]---------[4]
               gap-lock
or

        [1]--------[2]---------[3]---------[4]
gap-lock                                      gap-lock
```

### 3. Next-key Locks (prevent phantom row)

A next-key lock is a combination of record-lock and gap-lock.

When a record is modified (update or delete), the record is locked (record-lock) and the gap before next index is also locked. (gap-lock)

```
update record [2]

[1]--------[2]---------[3]---------[4]
      record-lock
               gap-lock
```

- 테이블에서 어떤 인덱스(pk포함)가 가장 큰 row를 update, delete할때 그보다 큰 값의 insert가 lock걸린다.
- trident id는 증가되는 값으로 발행된다.

따라서 최신에 발급된 trident id ”A"에 대해 trident id가 index(PK포함)인 row를 update를 할 경우 그보다 나중에 가입된 유저의(”A"보다 더 큰) 데이터의 insert가 lock이 걸린다.

신규유저 생성시 오래걸릴 경우 다음생성되는 유저도 lock이 걸려 확인한 건데, 유저 생성시 update도 있기 때문에 lock이 걸렸네요.
