# Parallel
```
https://docs.spring.io/spring-batch/docs/current/reference/html/scalability.html
https://jojoldu.tistory.com/550
https://jojoldu.tistory.com/493
```

## Partitioner
- 하나의 Step 을 gridSize 만큼 각각의 StepExecution 으로 나눠서 수행합니다
- StepExecution 이 독립적이므로, reader/processor/writer 의 thread-safe 불필요 합니다

```java
public class TestJobConfig {
  @Bean
  public Job testJob() {
    return jobBuilders.get("testJob")
      .start(partitionStep())
      .build();
  }

  // partition step, chunk 수행단위의 context 를 생성한다.
  @Bean
  @JobScope
  public Step partitionStep() {
    return stepBuilders.get("partitionStep")
      .partitioner("partitionStep", partitioner(null))
      // partitionHandler 에서 지정하거나
      .partitionHandler(partitionHandler())
      // step-level 에서 직접 지정할수있음
      .gridSize(10)
      .taskExecutor(asyncTaskExecutor)
      .step(partialProcessStep())
      .build();
  }
  
  @Bean
  @StepScope
  public Partitioner partitioner(
    @Value("#{jobExecutionContext[total]}") Integer totalCount) {

    Map<String, ExecutionContext> partitionMap = new HashMap<>();

    return gridSize -> {
      // paging param 생성
      int pageSize = (totalCount + gridSize);

      for (int i = 0; i < gridSize; i++) {
        ExecutionContext executionContext = new ExecutionContext();
        executionContext.putInt("offset", i * pageSize);
        executionContext.putInt("size", pageSize);

        // key 가 중복되지만 않으면됨
        partitionMap.put("partition-" + i, executionContext);
      }

      return partitionMap;
    };
  }
  
  @Bean
  @StepScope
  public PartitionHandler partitionHandler() {
    TaskExecutorPartitionHandler partitionHandler = new TaskExecutorPartitionHandler();
    // step-build 시점에 설정도 가능함. 대신 여기에서하면 JobParameter 를 받아서 runtime 으로 지정가능
    partitionHandler.setStep(partialProcessStep());
    partitionHandler.setTaskExecutor(asyncTaskExecutor);
    partitionHandler.setGridSize(10);

    return partitionHandler
  }
  
  // 전달된 chunk item 의 read/write step
  @Bean
  @JobScope
  public Step partialProcessStep() {
    return stepBuilders.get("partialProcessStep")
      .<Object, Object>chunk(1000)
      .reader(partialReader(null, null))
      .writer(partialWriter())
      .build();
  }

  @Bean
  @StepScope
  public ItemReader<List<Object>> partialReader(
    @Value("#{stepExecutionContext[offset]}") Integer offset,
    @Value("#{stepExecutionContext[size]}") Integer size) {

    Page<Long> pagedIds = testRepository.findIdsByPageable(new PageRequest(offset, size));

    return () -> {
      List<Long> ids = pagedIds.getContent();

      return testService.searchByIds(ids);
    };
  }

  @Bean
  @StepScope
  public ItemWriter<Object> partialWriter() {
    return items -> testRepository.save(items);
  }
}
```

## Multi-thread
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