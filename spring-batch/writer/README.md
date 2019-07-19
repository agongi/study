## Writer

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/339?category=635883
- http://www.mybatis.org/spring/batch.html
```

#### JdbcBatchItemWriter

```java
@Bean
public JdbcBatchItemWriter<Pay> jdbcBatchItemWriter() {
    return new JdbcBatchItemWriterBuilder<Pay>() // POJO
        .dataSource(dataSource)
        .sql("insert into pay(id, amount) values (:id, :amount)")
        .beanMapped() // beanMapper
        .build();
}

@Bean
public JdbcBatchItemWriter<Pay> jdbcBatchItemWriter() {
	new JdbcBatchItemWriterBuilder<Map<String, Object>>() // Map
	    .dataSource(this.dataSource)
        .sql("insert into pay(id, amount) values (:id, :amount)")
        .columnMapped()	// columnMapper
	    .build();
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
        log.info("writer");

        itemWriter.write(items);
    };
}
```

#### HibernateItemWriter

#### JpaItemWriter

#### [MyBatisBatchItemWriter](http://www.mybatis.org/spring/batch.html)
