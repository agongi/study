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

  - column 명과 fieldName 이 다르다면, 조회결과를 field 과 동일하게 alias 설정이 필요하다

    ```sql
    SELECT USER_ID as id ....
    ```

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

아니면 직접구현할 수 있다.

```java
// out
itemReader.setRowMapper((rs, rowNum) -> {
  Channel item = new WindowChannel();
  item.setId(rs.getLong("USER_ID"));
  item.setName(rs.getString("USER_NM"));
  item.setUserAddress(JacksonUtils.toObject(rs.getString("USER_ADDR"), UserAddress.class));
  
  return item;
});
```

HibernateCursorItemReader

StoredProcedureItemReader

### Paging

#### JdbcPagingItemReader

HibernatePagingItemReader

JpaPagingItemReader

### File

#### FlatFileItemWriter

Read from file

```java
@Bean
@StepScope
public FlatFileItemReader<User> fileReader() {
  DefaultLineMapper<User> defaultLineMapper = new DefaultLineMapper<>();
  defaultLineMapper.setFieldSetMapper(new UserFieldSetMapper());
  defaultLineMapper.setLineTokenizer(new DelimitedLineTokenizer(","));

  FlatFileItemReader<User> userFileReader = new FlatFileItemReader();
  userFileReader.setLineMapper(defaultLineMapper);
  userFileReader.setEncoding(StandardCharsets.UTF_8.name());
  userFileReader.setResource(resourceLoader.getResource(directoryPath));

  return userFileReader;
}

public class UserFieldSetMapper implements FieldSetMapper<User> {
  @Override
  public SellerSaleStats mapFieldSet(FieldSet fieldSet) throws BindException {
    return User.builder()
      .id(fieldSet.readLong(1))
      .name(fieldSet.readString(2))
      .age(fieldSet.readInt(3))
      .build();
  }
}
```

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