## JdbcTemplate

```
ㅁ Author: suktae.choi
ㅁ References:
- https://github.com/benelog/spring-jdbc-tips/blob/master/spring-jdbc-core.md
- https://blog.outsider.ne.kr/882
- https://homoefficio.github.io/2020/01/25/Spring-Data%EC%97%90%EC%84%9C-Batch-Insert-%EC%B5%9C%EC%A0%81%ED%99%94/
- https://www.baeldung.com/spring-jdbc-autogenerated-keys
```

#### Index

- [querydsl 로 nativeSQL 생성하기](nativesql-using-querydsl)

***

Spring 에서 JDBC 의 편리한 사용을 위한 template 을 제공합니다.

> RestTemplate 같이 사용성을 위해 제공하는 template

제공되는 template 의 종류는 아래와 같습니다.

- JdbcTemplate
  - ("select id, name from user where id = ?", user.getId());
- **NamedParameterJdbcTemplate**
  - ("select id, name from user where id = :id", user.getId());
- SimpleJdbcTemplate
- Simple

바인딩되는 field 명을 직접 지정하는 방식인 NamedParameterJdbcTemplate 을 메인으로 설명하겠습니다.

설정법은 매우 간단합니다. `datasource` 를 전달만 하면됩니다.

```java
NamedParameterJdbcOperations jdbc = new NamedParameterJdbcTemplate(dataSource);
jdbc.execute("sql", params);
```

## SqlParameterSource

Query parameter 매핑

### BeanPropertySqlParameterSource

Object to params

```java
UserSearchCriteria criteria = new UserSearchCriteria();
criteria.setName("test");
criteria.setAge(99);

SqlParameterSource paramSource = new BeanPropertySqlParameterSource(criteria);

jdbcTemplate.queryForObject(
  "select name, age from user where name = :name and age = :age",
  paramSource, null);
```

TypeConversion 이 필요하다면, 직접상속후 #getValue 에서 타입마다 지정을 해주면된다.

```java
static class CustomSqlParameterSource extends BeanPropertySqlParameterSource {
  public CustomSqlParameterSource(Object object) {
    super(object);
  }

  @Override
  public Object getValue(String paramName) throws IllegalArgumentException {
    Object value = super.getValue(paramName);
    // conversion: zoned to instant (timestamp)
    if (value instanceof ZonedDateTime) {
      value = Timestamp.from(((ZonedDateTime) value).toInstant());
    }
    
    return value;
  }
}
```

### MapSqlParameterSource

Map to params

```java
Map<String, Object> paramMap = new HashMap<>();
paramMap.put("name", "test");
paramMap.put("age", 99);

SqlParameterSource paramSource = new MapSqlParameterSource(paramMap);

jdbcTemplate.queryForObject(
  "select name, age from user where name = :name and age = :age",
  paramSource, null);
```

## RowMapper

Query 결과 매핑

### BeanPropertyRowMapper

Result to object.

> snake_case -> camelCase. 필드명이 상이하면 `as` 를 통해 rename 이 필요하다.

```java
UserSearchCriteria criteria = new UserSearchCriteria();
criteria.setName("test");
criteria.setAge(99);
// where clause
SqlParameterSource paramSource = new BeanPropertySqlParameterSource(criteria);
// result map
RowMapper<User> userMapper = BeanPropertyRowMapper.newInstance(User.class)

  User user = jdbcTemplate.queryForObject(
  "select name, age from user where name = :name and age = :age",
  paramSource, userMapper);
```

TypeConversion 이 필요하다면 #setConversionService 로 converter 를 넘겨주고, rowMapper 를 한번 정의 후 재사용한다.

```java
public class ZonedDateTimeConverter implements Converter<Timestamp, ZonedDateTime> {
	@Override
	public ZonedDateTime convert(Timestamp source) {
		return ZonedDateTime.ofInstant(source.toInstant(), ZoneId.of("UTC"));
	}
}

public static <T> RowMapper<T> getCustomRowMapper(Class<T> mappedClass) {
  BeanPropertyRowMapper<T> mapper = BeanPropertyRowMapper.newInstance(mappedClass);
  // conversion service
  DefaultConversionService service = new DefaultConversionService();
  service.addConverter(new ZonedDateTimeConverter());
  // replace default
  mapper.setConversionService(service);
  return mapper;
}

public static void main(String[] args) {
  RowMapper<User> mapper = getCustomRowMapper(User.class);
  // ...
}
```

### ColumnMapRowMapper

Result to Map

```java
Map<String, Object> paramMap = new HashMap<>();
paramMap.put("name", "test");
paramMap.put("age", 99);
// where clause
SqlParameterSource paramSource = new MapSqlParameterSource(paramMap);
// result map
RowMapper<Map<String,Object>> userMapper = new ColumnMapRowMapper();

Map<String, Object> userMap = jdbcTemplate.queryForObject(
  "select name, age from user where name = :name and age = :age",
  paramSource, userMapper);
```

### Custom

마땅한게 없으면 직접 interface 를 구현해서 사용합니다. @FunctionalInterface 이므로 lambda 로 처리 가능합니다.

```java
RowMapper<User> userMapper = (rs, rowNum) -> {
  User user = new User();
  product.setId(rs.getInt("id"));
  product.setName(rs.getString("name"));
  product.setDescription(rs.getString("age"));
  return user;
};
```

## Auto Increment (or Sequence)

jdbcTemplate#update 를 사용하고 keyHolder 를 전달하면, 응답값으로 id 를 가져올수 있다. 하지만 복잡하므로

SimpleJdbcInsert 를 사용하는 방법을 알아보자:

- usingGeneratedKeyColumns: @Id 항목을 지정
- executeAndReturnKey: insert 하고 응답으로 `usingGeneratedKeyColumns` 에 지정한 컬럼을 받음

```java
SimpleJdbcInsert insertion = new SimpleJdbcInsert(dataSource)
  .withTableName("user")
  .usingGeneratedKeyColumns("id");

Map<String, Object> params = new HashMap<>();
params.put("name", "test");
params.put("age", 99);

Number id = insertion.executeAndReturnKey(params);
```

## Batch

spring-batch commit-interval 와 동일한 개념으로, N 개의 항목을 한번에 처리한다.

```java
@Override
public void update(List<? extends User> items) {
  jdbcTemplate.batchUpdate("update USER set name = ?, age = ? where id = ?", new BatchPreparedStatementSetter() {
    @Override
    public void setValues(PreparedStatement ps, int index) {
      User user = items.get(index);
      ps.setString(1, user.getName());
      ps.setInt(2, user.getAge());
      ps.setLong(3, user.getId());
    }

    @Override
    public int getBatchSize() {
      return items.size();
    }
  });
}
```

## Advanced

### [Lazy-Loading](https://github.com/benelog/spring-jdbc-tips/blob/master/lazy-loading.md)

ORM 에서는 fetch = FetchType.LAZY 으로 간단하게 설정가능하지만, jdbc 에서는 트리키하게 해결이 필요함

### Caching

이것까지 쓰진 않음

### [OneToMany](https://github.com/benelog/spring-jdbc-tips/blob/master/spring-jdbc-extensions.md)

... 이런거 들어가면 마땅히 좋은 방법이 없음. ORM 으로 하자

### Stored Procedure

```java
jdbcTemplate.execute("{CALL USER_STORED_PROCEDURE()}", o -> o.execute());
```
