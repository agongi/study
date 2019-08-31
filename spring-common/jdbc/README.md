## JDBC

```
ㅁ Author: suktae.choi
ㅁ References:
- https://blog.outsider.ne.kr/882
```

### Cores

Datasource 를 주입받아 row-level 에서 CRUD 를 수행하는 spring-template wrapper.

Here is popular templates:

- JdbcTemplate
- **NamedParameterJdbcTemplate**
- SimpleJdbcTemplate

#### JdbcTemplate

Basic implementation for JDBC operations.

The placeholders are marked as `?`, and the parameters are binded in insert-order.

**Select**

select primitives.

```java
String name = jdbcTemplate.queryForObject(
  "select name from user where id = ?",
  new Object[]{123456L},
  String.class);
```

select as object.

```java
jdbcTemplate.queryForObject(
  "select name, age from user where id = ?",
  new Object[]{123456L},
  new RowMapper<User>() {
    public User mapRow(ResultSet rs, int rowNum) throws SQLException {
      User user = new User();
      user.setName(rs.getString("name"));
      user.setAge(rs.getInt("age"));

      return user;
    }
  });
```

select a collection.

```java
jdbcTemplate.queryForList(
  "select name, age from user where id = ?",
  new Object[]{123456L},
  new RowMapper<User>() {
    public User mapRow(ResultSet rs, int rowNum) throws SQLException {
      User user = new User();
      user.setName(rs.getString("name"));
      user.setAge(rs.getInt("age"));

      return user;
    }
  });
```

**Insert/Update/Delete**

All are the same.

```java
jdbcTemplate.update("insert into user (name, age) values (?, ?)",  "test", 99);
jdbcTemplate.update("delete from user where id = ?",  123456L);
```

#### NamedParameterJdbcTemplate

It functions perfectly with JdbcTemplate but the placeholder is replaced to `:{name}` for easy distinguishment.

**MapSqlParameterSource**

bind map.

```java
Map<String, Object> paramMap = new HashMap<>();
paramMap.put("name", "test");
paramMap.put("age", 99);

SqlParameterSource paramSource = new MapSqlParameterSource(paramMap);

return jdbcTemplate.queryForObject(
  "select name, age from user where name = :name and age = :age",
  paramSource);
```

**BeanPropertySqlParameterSource**

bind object.

```java
UserSearchCriteria criteria = new UserSearchCriteria();
criteria.setName("test");
criteria.setAge(99);

SqlParameterSource paramSource = new BeanPropertySqlParameterSource(criteria);

return jdbcTemplate.queryForObject(
  "select name, age from user where name = :name and age = :age",
  paramSource);
```

### Batch

Batch commands is a cost-efficient way to execute sql multiple times in batch processing.

> It means It can reduce RTT

```java
@Override
public void update(List<? extends User> items) {
  jdbcTemplate.batchUpdate("update USER set name = ?, mod_ymdt = ? where id = ?", new BatchPreparedStatementSetter() {
    @Override
    public void setValues(PreparedStatement preparedStatement, int index) throws SQLException {
      User item = items.get(index);
      preparedStatement.setString(1, item.getId());
      preparedStatement.setTimestamp(2, new Date());
    }

    @Override
    public int getBatchSize() {
      return items.size();
    }
  });
}
```

### Stored Procedure

```java
jdbcTemplate.execute("{CALL USER_STORED_PROCEDURE()}", o -> o.execute());
```

