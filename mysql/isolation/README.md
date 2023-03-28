# Isolation

```
@author: suktae.choi
- https://dev.mysql.com/doc/refman/8.0/en/innodb-transaction-isolation-levels.html
- https://dev.mysql.com/doc/refman/8.0/en/innodb-locking-reads.html
```

## 증상
### DIRTY READ
커밋되지 않은 내용까지 조회되는 현상 입니다

### NON-REPEATABLE READ
transaction 도중 다른 tx 의 **[update 가 반영되어]** 재조회 했을때 결과가 달라지는 현상 입니다

### PHANTOM READ
transaction 도중 다른 tx 의 **[insert 가 반영되어]** 재조회 했을때 결과가 달라지는 현상 입니다

## 종류
### READ UNCOMMITTED
DIRTY READ + NON-REPEATABLE READ + PHANTOM READ 발생가능

### READ COMMITTED
NON-REPEATABLE READ + PHANTOM READ 발생가능

### REPEATABLE READ
PHANTOM READ 발생가능
  
- mysql 은 REPEATABLE READ 에도 gap/next-key lock 을 잡아서 PHANTOM READ 가 발생하지 않습니다

### SERIALIZABLE
모든 select 가 select ... for share 로 변경됩니다 (모든 증상이 발생하지 않음)

> 즉 select 실행시 DML 은 대기함
