## Duplicate Primary Key

```
ㅁ Author: suktae.choi
- http://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html
- https://dev.mysql.com/doc/refman/5.7/en/insert-on-duplicate.html
- http://stackoverflow.com/questions/2714587/mysql-on-duplicate-key-update-for-multiple-rows-insert-in-single-query
```

### Duplicate key strategy
| Category                                                              | Features                                    |
|-----------------------------------------------------------------------|---------------------------------------------|
| INSERT **IGNORE** INTO person VALUES ('', '')                             | Hold old record                             |
| **REPLACE** INTO person VALUES ('', '')                                   | Replace old to new record                   |
| INSERT INTO person VALUES ('', '') **ON DUPLICATE KEY UPDATE** id = #{id} | Update user specified value and hold others |

