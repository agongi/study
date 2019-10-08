## Writer

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/339?category=635883
- http://www.mybatis.org/spring/batch.html
```

### JDBC

#### JdbcBatchItemWriter

JDBC level 에서의 itemWriter 로, parameter 를 Object/Map 으로 전달하여 sql 을 수행한다.

NamedParameterJdbcTemplate 에 사용되는 parameterSource:

- BeanPropertySqlParameterSource
- MapSqlParameterSource

을 내부적으로 생성하는 Provider 를 전달한다.

```java
@Bean
public JdbcBatchItemWriter<Pay> jdbcBatchItemWriter() {
  JdbcBatchItemWriter<T> writer = new JdbcBatchItemWriter<>();
  // BeanPropertySqlParameterSource 을 전달하는 Provider
  writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<>());
  writer.setDataSource(dataSource);
  writer.setSql("insert into pay(id, amount) values (:id, :amount)");

  return writer;
}
```

Invoke writer.**afterPropertiesSet()** just after creation of writer is a good-pattern to validate all settings are correct.

```java
@Bean
@StepScope
public ItemWriter<Map<String, Object>> writer(
  @Value("#{jobExecutionContext['date']}") Date date) {

  JdbcBatchItemWriter<Map<String, Object>> itemWriter = new JdbcBatchItemWriter<>();
  // ...
  
  itemWriter.afterPropertiesSet(); // validate

  return items -> {
    itemWriter.write(items);
  };
}
```

#### HibernateItemWriter

#### JpaItemWriter

#### [MyBatisBatchItemWriter](http://www.mybatis.org/spring/batch.html)

### File

#### FlatFileItemWriter

