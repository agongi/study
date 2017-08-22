## Locks

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.08.23
ㅁ References:
 - https://dev.mysql.com/doc/refman/5.7/en/innodb-locking.html
 - https://dev.mysql.com/doc/refman/5.7/en/innodb-next-key-locking.html
```

#### Record Locks
- locks exact one record literally
- prevent **unrepeatable read**

```
update record [2]

[1]--------[2]---------[3]---------[4]
      record-lock
```

#### Gap Locks
- A lock on a gap between index records, or a lock on the gap **before the first** or **after the last** index record

```
update record [2]

[1]--------[2]---------[3]---------[4]
               gap-lock
or

        [1]--------[2]---------[3]---------[4]
gap-lock                                      gap-lock
```

#### Next-key Locks
- record lock + gap lock
- prevent **phantom read**

```
update record [2]

[1]--------[2]---------[3]---------[4]
      record-lock
   gap-lock   gap-lock
```


- 테이블에서 어떤 인덱스(pk포함)가 가장 큰 row를 update, delete할때 그보다 큰 값의 insert가 lock 걸린다
- id는 증가되는 값으로 발행된다
  - 최신에 발급된 id "A"에 대해 index (PK 포함)인 row를 update를 할 경우, 그보다 나중에 가입된 유저의("A"보다 더 큰) 데이터의 insert가 lock이 걸린다.

신규유저 생성시 오래걸릴 경우 다음생성되는 유저도 lock이 걸려 확인한 건데, 유저 생성시 update도 있기 때문에 lock이 걸림
