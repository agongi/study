# Flow
```
https://jojoldu.tistory.com/328
https://n1tjrgns.tistory.com/169
```

Job 에서 조건에 따른 Step 실행이 필요할때, Flow 를 사용합니다.

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

Flow 는 @StepScope 를 써야합니다...

eager 로 만들어버려서, scopedBean 을 Flow 에선 참조못함.
