## Isolation

```
ㅁ Author: suktae.choi
- https://dev.mysql.com/doc/refman/5.7/en/innodb-transaction-isolation-levels.html
- https://dev.mysql.com/doc/refman/5.7/en/innodb-consistent-read.html
- https://dev.mysql.com/doc/refman/5.7/en/innodb-locking-reads.html
- http://blog.sapzil.org/2017/04/01/do-not-trust-sql-transaction/
- http://arisu1000.tistory.com/27756
- http://notemusic.tistory.com/49
```

### Read Phenomena
#### dirty reads
- See uncommitted data

#### non-repeatable reads
- transaction 도중 다른 트랜잭션의 **[update 로]** select 결과가 달라지는 현상

#### phantom reads
- transaction 도중 다른 트랜잭션의 **[insert or delete 로]** select 결과가 달라지는 현상

### Isolation Levels
#### READ UNCOMMITTED
- .. All read phenomena!

#### READ COMMITTED
- No dirty reads
- Inconsistent read
  - Non locking read can the see data that is committed by another transaction after the current transaction started
- Locks level
  - record lock - update .. where, delete .. where, [locking read (select .. for update)](https://dev.mysql.com/doc/refman/5.7/en/innodb-locking-reads.html), lock in share mode
  - gap locks, next-key locks - not supported (== phantom reads occur)

<img src="images/Screen%20Shot%202017-08-23%20at%2002.37.01.png" width="75%">

#### REPEATABLE READ
- This is the `default` isolation level for InnoDB
- No dirty reads
- [Consistent read](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_consistent_read)
  - [Non locking read](https://dev.mysql.com/doc/refman/5.7/en/innodb-consistent-read.html) within the same transaction read snapshot established by the first regardless of changes performed by other transactions running at the same time
  - This can be done by reading [undo log](https://dev.mysql.com/doc/refman/5.7/en/glossary.html#glos_undo_log)
- Locks level
  - record locks - update .. where, delete .. where
  - gap locks, next-key locks - locking read, lock in share mode

#### SERIALIZABLE
- ... Strict!
