## Flow

```
ㅁ Author: suktae.choi
- https://jojoldu.tistory.com/328
- https://n1tjrgns.tistory.com/169
```

Job 에서 조건에 따른 Step 실행이 필요할때, Flow 를 사용한다.

```java
@Configuration
public class TestJobConfig {
  @Bean
  public Job testJob() {
    return jobBuilders.get("testJob")
      .start(noExecutionStep())
      .next(anyDecider())
      .from(anyDecider()).on(QUERY_EXECUTION_PHASE)
	      .to(amlDPSNTargetsApplyStep(expParam()))
  	    .next(amlDBSNPrivateTargetsApplyStep(expParam()))
    	  .next(amlDBSNCorporateTargetsApplyStep(expParam()))
      .from(anyDecider()).on(MANUAL_EXECUTION_PHASE)
      	.to(amlManualTargetApplyStep(expParam()))
      .end()
      .build();
  }

  @Bean
  public JobExecutionDecider anyDecider() {
    return (jobExecution, stepExecution) -> {
      Object searchById = testRepository.findById(1234L);

      return Objects.isNull(searchById) ? 
        FlowExecutionStatus.FAILED : new FlowExecutionStatus("GO_AHEAD");
    };
  }

  private Step noExecutionStep() {
    return stepBuilders.get("noExecutionStep")
      .tasklet((contribution, chunkContext) -> RepeatStatus.FINISHED)
      .build();
  }
}
```

빈 scope 에 대한 동작의 차이점 정리

\- https://blog.leocat.kr/notes/2020/06/10/spring-batch-scope-setting-when-using-partition

\- https://velog.io/@max9106/Spring-Bean%EC%9D%98-scope-dsk5mf4zbp



@JobScope는 Step 선언문에서 사용 가능하고, @StepScope는 Tasklet이나 ItemReader, ItemWriter, ItemProcessor에서 사용할 수 있습니다.

Flow 는 @StepScope 를 써야합니다_-;;;



eager 로 만들어버려서, scopedBean 을 Flow 에선 참조못함.

#### 



