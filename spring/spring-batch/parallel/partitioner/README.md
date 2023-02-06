## Partitioner

```
@author: suktae.choi
- https://www.baeldung.com/spring-batch-partitioner
- https://jojoldu.tistory.com/550?category=902551
- https://jobjava00.github.io/language/java/framework/spring-batch/partitioner/
```

- 하나의 Step 을 gridSize 만큼 각각의 StepExecution 으로 나눠서 수행 (독립적)
- StepExecution 이 독립적이므로, reader/processor/writer 의 thread-safe 불필요

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
