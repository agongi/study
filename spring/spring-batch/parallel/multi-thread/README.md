## Multi-thread

```
ㅁ Author: suktae.choi
ㅁ References:
- https://jojoldu.tistory.com/493
```

- 하나의 Step 에서 chunk 단위로 병렬수행
- StepExecution 을 공유하므로, reader/processor/writer 의 thread-safe 필요
  - CursorItemReader 의 경우 thread-safe 하지 않으므로, 중복으로 읽을수 있음

