## Spring Batch

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring-batch/trunk/reference/htmlsingle
- http://becko.tistory.com/category/Spring-Batch
- https://jojoldu.tistory.com/search/spring%20batch
```

#### Index
- [Reader](reader)
- [Writer](writer)
- [Processor](processor)
- [Flow](flow)
- [Partitioner](partitioner)
- Listener

#### Blog
- [Spring Batch commit-interval에 대한 정리](http://sheerheart.tistory.com/entry/Spring-Batch-commitinterval%EC%97%90-%EB%8C%80%ED%95%9C-%EC%A0%95%EB%A6%AC)
- [Tasklet vs Reader/Processor/Writer](http://www.baeldung.com/spring-batch-tasklet-chunk)
- [JpaItemReader 및 MongoItemReader 활용](http://devjms.tistory.com/72)
- [ExecutionContext 및 @JobScope, @StepScope](https://jojoldu.tistory.com/330)
- [ItemReader 리턴타입](https://jojoldu.tistory.com/132)
- [Flow 활용](https://jojoldu.tistory.com/328)

### Cores

#### Tasklet vs Reader/Processor/Writer

- R/P/W is **chunk-based** (== commit-internal) processing
- Tasklet is bundle of these (== turnkey)

#### Job

- How to stop job once condition meet?

```java
.listener(new JobExecutionListenerSupport() {
  @Override
  public void beforeJob(JobExecution jobExecution) {
    if (/* exitCondition: true */) {
      jobExecution.stop();
    }
  }
})
```

#### Step

- How many `commitCount` does it happen?

```java
.listener(new ChunkListenerSupport() {
  @Override
  public void afterChunk(ChunkContext context) {
    StepExecution stepExecution = context.getStepContext().getStepExecution();
    
    if (/* exitCondition: true */) {
      stepExecution.setTerminateOnly();
    }
  }
})
```

