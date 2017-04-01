## Duplicated Primary Key

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.06
ㅁ References:
 - http://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html
 - https://dev.mysql.com/doc/refman/5.7/en/insert-on-duplicate.html
 - http://stackoverflow.com/questions/2714587/mysql-on-duplicate-key-update-for-multiple-rows-insert-in-single-query
```

### Duplicated key strategy

| Category                                                              | Features                                    |
|-----------------------------------------------------------------------|---------------------------------------------|
| INSERT **IGNORE** INTO person VALUES ('', '')                             | Hold old record                             |
| **REPLACE** INTO person VALUES ('', '')                                   | Replace old to new record                   |
| INSERT INTO person VALUES ('', '') **ON DUPLICATE KEY UPDATE** id = #{id} | Update user specified value and hold others |

### on duplicated key update multiple rows
```sql
INSERT INTO
  beautiful (name, age)
VALUES
  ('Helen', 24),
  ('Katrina', 21),
  ('Samia', 22),
  ('Hui Ling', 25),
  ('Yumie', 29)
ON DUPLICATE KEY UPDATE
  age = VALUES(age),
  name = VALUES(name)
```
