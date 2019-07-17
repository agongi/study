## Flow

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:rte2:brte:batch_core:flow_control
```

Job 에서 조건에 따라 Step 의 실행자체를 결정필요할때, 분기처리가 가능하다

```java
public class TestJobConfig {
  @Bean
  public Job testJob() {
    return jobBuilders.get(JOB_NAME)
      .start(new FlowBuilder<Flow>("testFlow")
             .start(decider()).on(FlowExecutionStatus.COMPLETED.getName())
	             .to(step1())
  	           .next(step2())
    	         .next(step3())
             .from(decider()).on(FlowExecutionStatus.FAILED.getName())
      	       .to(step10())
             .end()).build()
      .build();
  }

  @Bean
  public JobExecutionDecider decider() {
    return (jobExecution, stepExecution) -> {
      Object searchById = testRepository.findById(1234L);

      return Objects.isNull(searchById) ? 
        FlowExecutionStatus.FAILED : FlowExecutionStatus.COMPLETED;
    };
  }
}
```



#### 



