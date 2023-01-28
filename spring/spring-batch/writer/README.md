## Writer

```
ㅁ Author: suktae.choi
- https://jojoldu.tistory.com/339?category=635883
- http://www.mybatis.org/spring/batch.html
```

### JDBC

#### JdbcBatchItemWriter

CUD query 의 params 을 전달한다:

- BeanPropertySqlParameterSource
  - asObject

```java
@Bean
public JdbcBatchItemWriter<UserVO> batchWriter() {
  JdbcBatchItemWriter<UserVO> writer = new JdbcBatchItemWriter<>();
  // jdbc
  writer.setDataSource(dataSource);
  // input
  writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>());
  writer.setSql("insert into user(id, country) values (:id, :country)");

  return writer;
}

private static class UserVO {
  private Long id;
  private CountryType country;
}
```

- MapSqlParameterSource
  - asMap

```java
@Bean
public JdbcBatchItemWriter<Map<String, Object>> batchWriter() {
  JdbcBatchItemWriter<Map<String, Object>> writer = new JdbcBatchItemWriter<>();
  // jdbc
  writer.setDataSource(dataSource);
  // input
  writer.setItemSqlParameterSourceProvider(new MapItemSqlParameterSourceProvider<>());
  writer.setSql("insert into user(id, country) values (:id, :country)");

  return writer;
}
```

HibernateItemWriter

JpaItemWriter

### File

#### FlatFileItemWriter

File write 의 schema 를 전달한다:

- PassThroughLineAggregator
  - fromType

```java
@Bean
public FlatFileItemWriter<Long> fileWriter() {
  FlatFileItemWriter<Long> itemWriter = new FlatFileItemWriter();
  // resource
  itemWriter.setResource(getResource());
  itemWriter.setEncoding(StandardCharsets.UTF_8.name());
  // output
  itemWriter.setLineAggregator(new PassThroughLineAggregator<>());

  return itemWriter;
}
```

- DelimitedLineAggregator
  - fromObject

```java
@Bean
public FlatFileItemWriter<UserVO> fileWriter() {
  FlatFileItemWriter<UserVO> itemWriter = new FlatFileItemWriter();
  // resource
  itemWriter.setResource(getResource());
  itemWriter.setEncoding(StandardCharsets.UTF_8.name());
  // output
  DelimitedLineAggregator<UserVO> lineAggregator = new DelimitedLineAggregator<>();
  lineAggregator.setDelimiter(",");
  lineAggregator.setFieldExtractor(item -> {
		List<String> result = new ArrayList<>();
		result.add(item.getId());
    result.add(item.getCountry());
    
    return result.toArray();
  });
  itemWriter.setLineAggregator(lineAggregator);

  return itemWriter;
}

@Getter
private static class UserVO {
  private Long id;
  private CountryType country;
}
```

### MyBatis

#### [MyBatisBatchItemWriter](http://www.mybatis.org/spring/batch.html)

