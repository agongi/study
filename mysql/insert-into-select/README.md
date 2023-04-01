# INSERT INTO SELECT

```
@author: suktae.choi
- https://www.w3schools.com/sql/sql_insert_into_select.asp
```

```sql
INSERT INTO
  table_a (column_a, column_b, column_c)
SELECT
  column_a, column_b, column_c
FROM
  table_b
WHERE
  id = #{id}
```
