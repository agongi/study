## Flow

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/328
- https://n1tjrgns.tistory.com/169
```

Job 에서 조건에 따른 Step 실행이 필요할때, Flow 를 사용한다.

```java
@Configuration
public class TestJobConfig {
  @Bean
  public Job testJob() {
    return jobBuilders.get("jobName")
      .start(new FlowBuilder<Flow>("testFlow")
      	.from(decider()).on(FlowExecutionStatus.COMPLETED.getName())	// listen
	      	.to(step1())	// if then 1st
  	      .next(step2())	// next 2nd
    	    .next(step3())	// next 3rd
				.from(decider()).on(FlowExecutionStatus.FAILED.getName())	// listen
					.to(step10())	// if then 1st
					.end().build())
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



