## Partitioner

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.baeldung.com/spring-batch-partitioner
- https://jobjava00.github.io/language/java/framework/spring-batch/partitioner/
```

Step 에서 처리할 chunk 를 분할해서 parallel 처리 가능하게함.

- 파니셔닝 Step
- partial items 의 read/write 수행 Step

으로 크게 구분된다.

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
  public Step partitionStep() {
    return stepBuilders.get("partitionStep")
      .partitioner("partitionStep", partitioner(null))
      // partitionHandler 에서 지정하거나
      .partitionHandler()
      // step-level 에서 직접 지정할수있음
      .gridSize(10)
      .taskExecutor(asyncTaskExecutor)
      .step(partialProcessStep())
      .build();
  }
  
  @Bean
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

        // key 가 중복되지만 않으면됨, sequence 사용이 추천됨
        partitionMap.put("partition-" + i, executionContext);
      }

      return partitionMap;
    };
  }
  
  @Bean
  public PartitionHandler partitionHandler() {
    TaskExecutorPartitionHandler partitionHandler = new TaskExecutorPartitionHandler();
    partitionHandler.setStep(partialProcessStep());
    partitionHandler.setTaskExecutor(asyncTaskExecutor);
    partitionHandler.setGridSize(10);

    return partitionHandler
  }
  
  // 전달된 chunk item 의 read/write step
  @Bean
  public Step partialProcessStep() {
    return stepBuilders.get("partialProcessStep")
      .<Object, Object>chunk(1000)
      .reader(partialReader(null, null))
      .writer(partialWriter())
      .build();
  }

  @Bean
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
  public ItemWriter<Object> partialWriter() {
    return items -> testRepository.save(items);
  }
}
```



#### 



