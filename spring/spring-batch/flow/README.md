## Flow

```
ㅁ Author: suktae.choi
ㅁ References:
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
      	.from(decider()).on(FlowExecutionStatus.COMPLETED.getName())
	      	.to(step1())
  	      .next(step2())
    	    .next(step3())
				.from(decider()).on(FlowExecutionStatus.FAILED.getName())
					.to(step10())
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



