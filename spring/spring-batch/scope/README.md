# Scope
```
https://jojoldu.tistory.com/493
```

빈 scope 에 대한 동작의 차이점 정리
https://blog.leocat.kr/notes/2020/06/10/spring-batch-scope-setting-when-using-partition
https://velog.io/@max9106/Spring-Bean%EC%9D%98-scope-dsk5mf4zbp

@JobScope는 Step 선언문에서 사용 가능하고, @StepScope는 Tasklet이나 ItemReader, ItemWriter, ItemProcessor에서 사용할 수 있습니다.