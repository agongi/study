# Duplicated record
```
@author: suktae.choi
- https://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html
```

신규 레코드를 insert 할때 3가지 방식이 존재합니다:

## ON DUPLICATE KEY UPDATE
키 중복 발생 시 (P.K or unique key) 업데이트 합니다.
```sql
mysql> INSERT INTO person VALUES ('James', 'Seoul')
    ON DUPLICATE KEY UPDATE address = 'Other city'

Query OK, 1 row affected (0.00 sec)
```

## REPLACE INTO
키 중복 발생 시 (P.K or unique key) 삭제 & 추가 합니다.
```sql
mysql> REPLACE INTO person VALUES ('James', 'Seoul');

Query OK, 2 rows affected (0.00 sec)
```

## INSERT IGNORE
키 중복 발생 시 (P.K or unique key) 무시 합니다.
```sql
mysql> INSERT IGNORE INTO person VALUES ('James', 'Seoul');

Query OK, 0 rows affected (0.00 sec)
```