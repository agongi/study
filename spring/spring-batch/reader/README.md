# Reader
```
https://jojoldu.tistory.com/336?category=635883
http://www.mybatis.org/spring/batch.html
```

## JDBC
## JdbcCursorItemReader
조회 query 의 params 을 전달합니다:
- ListPreparedStatementSetter

```java
@Bean
public JdbcCursorItemReader cursorReader() {
  ListPreparedStatementSetter statementSetter = new ListPreparedStatementSetter();
  statementSetter.setParameters(Lists.newArrayList("KR", "SEOUL"));

  JdbcCursorItemReader itemReader = new JdbcCursorItemReader<>();
  // jdbc
  itemReader.setDataSource(dataSource);
  itemReader.setFetchSize(chunkSize);
  // input
  itemReader.setPreparedStatementSetter(statementSetter);
  itemReader.setSql("SELECT id FROM user WHERE ctr_tp_cd = ? AND rg_tp_cd = ?");
  // omitted ...
}
```

- ArgumentPreparedStatementSetter

```java
@Bean
public JdbcCursorItemReader cursorReader() {
  ArgumentPreparedStatementSetter statementSetter = new ArgumentPreparedStatementSetter(Lists.newArrayList("KR", "SEOUL").toArray());

  JdbcCursorItemReader itemReader = new JdbcCursorItemReader<>();
  // jdbc
  itemReader.setDataSource(dataSource);
  itemReader.setFetchSize(chunkSize);
  // input
  itemReader.setPreparedStatementSetter(statementSetter);
  itemReader.setSql("SELECT id FROM user WHERE ctr_tp_cd = ? AND rg_tp_cd = ?");
  // omitted ...
}
```

조회 결과는 Object/NativeType/Map 으로 반환합니다:
- BeanPropertyRowMapper

```java
@Bean
public JdbcCursorItemReader<UserVO> cursorReader() {
  ListPreparedStatementSetter statementSetter = new ListPreparedStatementSetter();
  statementSetter.setParameters(Lists.newArrayList("KR", "SEOUL"));

  JdbcCursorItemReader<UserVO> itemReader = new JdbcCursorItemReader<>();
  // jdbc
  itemReader.setDataSource(dataSource);
  itemReader.setFetchSize(chunkSize);
  // input
  itemReader.setPreparedStatementSetter(statementSetter);
	itemReader.setSql("SELECT id, ctr_tp_cd as country, rg_tp_cd region\n"
            + "FROM user\n" 
            + "WHERE ctr_tp_cd = ? AND rg_tp_cd = ?");
  // output
	itemReader.setRowMapper(new BeanPropertyRowMapper<>(UserVO.class));
}

private static class UserVO {
  private Long id;
  private CountryType country;
  private RegionType region;
}
```

> Column 과 field 이름이 다르다면, 조회결과 매핑을 위해 `alias 지정` 필요

- SingleColumnRowMapper

```java
@Bean
public JdbcCursorItemReader<Long> cursorReader() {
  ListPreparedStatementSetter statementSetter = new ListPreparedStatementSetter();
  statementSetter.setParameters(Lists.newArrayList("KR", "SEOUL"));

  JdbcCursorItemReader<Long> itemReader = new JdbcCursorItemReader<>();
  // jdbc
  itemReader.setDataSource(dataSource);
  itemReader.setFetchSize(chunkSize);
  // input
  itemReader.setPreparedStatementSetter(statementSetter);
	itemReader.setSql("SELECT id\n"
            + "FROM user\n" 
            + "WHERE ctr_tp_cd = ? AND rg_tp_cd = ?");
  // output
	itemReader.setRowMapper(new SingleColumnRowMapper<>(Long.class));
}
```

- ColumnMapRowMapper

```java
@Bean
public JdbcCursorItemReader<Map<String, Object>> cursorReader() {
  ListPreparedStatementSetter statementSetter = new ListPreparedStatementSetter();
  statementSetter.setParameters(Lists.newArrayList("KR", "SEOUL"));

  JdbcCursorItemReader<Map<String, Object>> itemReader = new JdbcCursorItemReader<>();
  // jdbc
  itemReader.setDataSource(dataSource);
  itemReader.setFetchSize(chunkSize);
  // input
  itemReader.setPreparedStatementSetter(statementSetter);
	itemReader.setSql("SELECT id, ctr_tp_cd, rg_tp_cd\n"
            + "FROM user\n" 
            + "WHERE ctr_tp_cd = ? AND rg_tp_cd = ?");
  // output
	itemReader.setRowMapper(new ColumnMapRowMapper());
}

// Map<String, Object>
item.get("id")
item.get("ctr_tp_cd")
item.get("rg_tp_cd")
```

## File
### FlatFileItemReader
Read 할 File 의 delimiter 방식을 지정합니다:

- DelimitedLineTokenizer

```java
@Bean
public FlatFileItemReader fileReader() {
  DefaultLineMapper defaultLineMapper = new DefaultLineMapper<>();
  // input
  defaultLineMapper.setLineTokenizer(new DelimitedLineTokenizer(","));
  // omitted ...

  FlatFileItemReader itemReader = new FlatFileItemReader<>();
  // resource
  itemReader.setEncoding(StandardCharsets.UTF_8.name());
  itemReader.setResource(getResource());
  // in/output
  itemReader.setLineMapper(defaultLineMapper);

  return itemReader;
}
```

- FixedLengthTokenizer
  - 특정 길이로 구분
- RegexLineTokenizer
  - 정규식으로 구분

조회 결과는 Object/NativeType/Map 으로 반환합니다:
- BeanWrapperFieldSetMapper
- ArrayFieldSetMapper
- PassThroughFieldSetMapper

하지만 lambda 직접 구현도 가능합니다:

```java
@Bean
public FlatFileItemReader<Map<String, Object>> fileReader() {
  DefaultLineMapper<Map<String, Object>> defaultLineMapper = new DefaultLineMapper<>();
  // input
  defaultLineMapper.setLineTokenizer(new DelimitedLineTokenizer(","));
  // output
  defaultLineMapper.setFieldSetMapper(fieldSet -> ImmutableMap.of(
    "id", fieldSet.readLong(0)
  ));

  FlatFileItemReader<Map<String, Object>> itemReader = new FlatFileItemReader<>();
  // resource
  itemReader.setEncoding(StandardCharsets.UTF_8.name());
  itemReader.setResource(getResource());
  // in/output
  itemReader.setLineMapper(defaultLineMapper);

  return itemReader;
}
```

```java
@Bean
public FlatFileItemReader<UserVO> fileReader() {
  DefaultLineMapper<UserVO> defaultLineMapper = new DefaultLineMapper<>();
  // input
  defaultLineMapper.setLineTokenizer(new DelimitedLineTokenizer(","));
  // output
  defaultLineMapper.setFieldSetMapper(fieldSet -> UserVO.builder()
		.id(fieldSet.readLong(0))
		.country(fieldSet.readString(1))
		.region(fieldSet.readString(2))
		.build());

  FlatFileItemReader<UserVO> itemReader = new FlatFileItemReader<>();
  // resource
  itemReader.setEncoding(StandardCharsets.UTF_8.name());
  itemReader.setResource(getResource());
  // in/output
  itemReader.setLineMapper(defaultLineMapper);

  return itemReader;
}

@Builder
private static class UserVO {
    private Long id;
    private String country;
    private String region;
}
```

## Simple
### ListItemReader
Simple reader of using service#method

```java
@Bean
public ItemReader<User> itemReader() {
  // List<User> UserService#getAllUsers
  return new ListItemReader(userService.getAllUsers());
}
```

### RepositoryItemReader
Simple reader of using repository#method

```java
@Bean
public ItemReader<User> itemReader() {
  RepositoryItemReader<User> reader = new RepositoryItemReader<>();
  reader.setRepository(userRepository);
  reader.setMethodName("getAllUsers");
  reader.setSort(Collections.singletonMap("id", Sort.Direction.ASC));

  List<Object> parameters = new ArrayList<>();
  parameters.add(new Date());

  reader.setArguments(parameters);

  return reader;
}
```

### Paging
- JdbcPagingItemReader
- HibernatePagingItemReader
- JpaPagingItemReader
