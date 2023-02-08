# Raft Consensus Algorithm

```
@author: suktae.choi
- https://d2.naver.com/helloworld/5663184
- https://yoongrammer.tistory.com/50
```

## 리더선출
- 특정시점까지 heartbeat 을 받지 못한다면 (leader to follower)
- 투표시작
  - follower 는 해당 회차에 한번의 투표만 가능
  - follower 는 random time-interval 로 투표 시작요청 (self-voted 상태로 투표요청)
  - follower 는 가장먼저 받은 투표요청에 vote
  - 중복되면 회차++ 해서 재수행

## 로그복제
writeConcern: majority 의 동작과 동일