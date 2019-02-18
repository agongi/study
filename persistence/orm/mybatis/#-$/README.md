## #{...} vs ${...}

```
ㅁ Author: suktae.choi
ㅁ References:
- http://marobiana.tistory.com/60
- https://github.com/mybatis/mybatis-3/wiki/FAQ#what-is-the-difference-between--and-
```

### #{value}
```sql
SELECT *
FROM USER
WHERE
  NAME = ? -- #{name}
```
Sql is cached as prepared statement in driver, and the parameter are set in `?`

- Used in value
  - column = #{value}
- double quotes are automatically marked
```sql
SELECT *
FROM USER
WHERE
  NAME = "james"
```

### ${column}
```sql
SELECT *
FROM ${table}
WHERE
  ${column} = "james"
```
It is bound in sql statement itself

- Used in field or table
  - ${column} = "value"
  - SELECT * FROM ${table}
- double quotes are not marked
```sql
SELECT *
FROM USER_COUNTRY
WHERE
  ID = "james"
```
