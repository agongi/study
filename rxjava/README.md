## RxJava

```
ㅁ Author: suktae.choi
ㅁ References:
- https://proandroiddev.com/understanding-rxjava-subscribeon-and-observeon-744b0c6a41ea
```

scheduler - thread pool

Schedulers.io()
Network IO 나 File IO 등을 처리하기 위한 쓰레드
Schedulers.computation()
단순 연산로직등에 사용되며 Event Looper 에 의해 동작하는 쓰레드
subscribeOn
데이터를 주입하는 시점에 대한 쓰레드 선언이며 모든 stream 내에서 최종적으로 선언한 쓰레드가 할당됩니다.
observeOn
쓰레드를 선언한 다음부터 새로운 쓰레드가 선언되기 전까지 데이터 처리에 동작할 쓰레드를 할당합니다.



subscribeOn() 은 체인 어디에 정의하든 상관없이 최상위에있는것처럼동작, 여러번정의해도 first one 만 적용 나머지는 무시
한번선택된 thread in Schedulers 가 혼자 처리한다 (thread assigned per requests, thread per flow)
-> flatmap 으로 pick-up 하도록 풀어줘야함
