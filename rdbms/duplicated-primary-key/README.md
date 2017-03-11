## Duplicated Primary Key

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.06
ㅁ References:
 - http://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html
 - https://dev.mysql.com/doc/refman/5.7/en/insert-on-duplicate.html
```

| Category                                                              | Features                                    |
|-----------------------------------------------------------------------|---------------------------------------------|
| INSERT **IGNORE** INTO person VALUES ('', '')                             | Hold old record                             |
| **REPLACE** INTO person VALUES ('', '')                                   | Replace old to new record                   |
| INSERT INTO person VALUES ('', '') **ON DUPLICATE KEY UPDATE** id = #{id} | Update user specified value and hold others |
