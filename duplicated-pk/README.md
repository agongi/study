## Duplicated P.K
There are some way to handle when duplicated p.k record is inserted in MySQL. This section may describe queries that is able to manager it in query level.

> INSERT **IGNORE** INTO holds old, **REPLACE** INTO replace to new and INSERT INTO VALUES **ON DUPLICATE KEY UPDATE** only updates user specified values while with holding other fields.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.06
ㅁ References:
 - http://jason-heo.github.io/mysql/2014/03/05/manage-dup-key2.html
```

| Category                                                              | Features                                    |
|-----------------------------------------------------------------------|---------------------------------------------|
| INSERT IGNORE INTO person VALUES ('', '')                             | Hold old record                             |
| REPLACE INTO person VALUES ('', '')                                   | Replace old to new record                   |
| INSERT INTO person VALUES ('', '') ON DUPLICATE KEY UPDATE id = #{id} | Update user specified value and hold others |
