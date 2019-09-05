## Reader

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/336?category=635883
- http://www.mybatis.org/spring/batch.html
```

### Cursor

#### JdbcCursorItemReader

조회쿼리의 params 은 Collection/Array 로 전달한다:

- ListPreparedStatementSetter
- ArgumentPreparedStatementSetter

sql 결과는 Object/Map 으로 반환한다:

- BeanPropertyRowMapper
- ColumnMapRowMapper

```java
@Bean
public JdbcCursorItemReader<Map<String, Object>> cursorReader() {
  ListPreparedStatementSetter statementSetter = new ListPreparedStatementSetter();
  statementSetter.setParameters(Lists.newArrayList(from, to));

  JdbcCursorItemReader<Map<String, Object>> itemReader = new JdbcCursorItemReader<>();
  itemReader.setDataSource(dataSource);
  itemReader.setFetchSize(1000);
  itemReader.setSql("select id, amount from pay where id >= ? and id < ?");
  // input
  itemReader.setPreparedStatementSetter(statementSetter);
  // output
  itemReader.setRowMapper(new ColumnMapRowMapper());

  return itemReader;
}
```

HibernateCursorItemReader

StoredProcedureItemReader

### Paging

#### JdbcPagingItemReader

HibernatePagingItemReader

JpaPagingItemReader

### File

FlatFileItemWriter

### Custom

#### ListItemReader

Simple reader of using service#method

```java
@Bean
@StepScope
public ItemReader<User> itemReader() {
  // List<User> UserService#getAllUsers
  return new ListItemReader(userService.getAllUsers());
}
```

#### RepositoryItemReader

Simple reader of using repository#method

```java
@Bean
@StepScope
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

MyBatisPagingItemReader

MyBatisCursorItemReader