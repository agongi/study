## Isolation
This section describes lock types used by InnoDB.

> Those lock mechanism is trade-off of performance and concurrency.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.07.29
ㅁ References:
 - https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html
 - http://ktdsoss.tistory.com/382
```

### 1. Factors
#### 1.1 Dirty Read
commit; 되기전 data 를 읽는 현상

#### 1.2 Unrepeatable Read
transaction 도중에, **[update 로 인해]** select 결과가 달라지는 현상

#### 1.3 Phantom Read
transaction 도중에, **[insert or delete 로 인해]** select 결과가 달라지는 현상

### 2. Locks
#### 2.1 Record Lock
- locks exact one record literally
- prevent **unrepeatable read**

```
update record [2]

[1]--------[2]---------[3]---------[4]
      record-lock
```

#### 2.2 Gap Lock
- locks before/after record

```
update record [2]

[1]--------[2]---------[3]---------[4]
               gap-lock
or

        [1]--------[2]---------[3]---------[4]
gap-lock                                      gap-lock
```

#### 2.3 Next-key Lock
- record lock + gap lock
- prevent **phantom read**

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

### 3. Levels
#### 3.1 READ UNCOMMITTED
- dirty read
- unrepeatable read
- phantom read

#### 3.2 READ COMMITTED
- unrepeatable read
- phantom read

#### 3.3 REPEATABLE READ
- none (with next-key lock)

#### 3.4 SERIALIZABLE
- none
