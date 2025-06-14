# Multi-thread
```
https://docs.spring.io/spring-batch/reference/scalability.html
https://jojoldu.tistory.com/493
```

- 동일 Step 에서 Chunk 단위의 처리를 (지정된 threadpool 을 통해) 병렬 수행합니다
- StepExecution 을 공유하므로, reader/processor/writer 의 thread-safe 필요하니다
- CursorItemReader 의 경우 thread-safe 하지 않으므로, 중복으로 읽을수 있으므로, 동기화 처리가 필요합니다
  - https://docs.spring.io/spring-batch/docs/current/api/org/springframework/batch/item/support/SynchronizedItemStreamReader.html 참고

```java
public TaskExecutor taskExecutor(int maxThreadCount) {
  ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
  taskExecutor.setCorePoolSize(maxThreadCount);
  taskExecutor.setMaxPoolSize(maxThreadCount);
  taskExecutor.setThreadNamePrefix("multi-thread-");
  taskExecutor.setWaitForTasksToCompleteOnShutdown(Boolean.TRUE);
  taskExecutor.setTaskDecorator(RequestContextTemplate::decorate);
  // validate
  taskExecutor.afterPropertiesSet();
  return taskExecutor;
}

@Bean
@JobScope
public Step roleCleansingStep(@Value("#{jobExecutionContext['" + CONTEXT_NAME + "']}") Properties properties) throws Exception {
    return new StepBuilder(methodName(), jobRepository)
        .<String, String>chunk(500, transactionManager)
        .reader(reader(properties))
        .writer(writer(properties))
        .listener(new ChunkListener() {
            @Override
            public void afterChunk(ChunkContext context) {
                // print progressing
                PrintUtils.print(context);
            }
        })
        .taskExecutor(taskExecutor(10))
        .build();
}
```