## Reactive Stream

```
ㅁ Author: suktae.choi
ㅁ References:
- https://proandroiddev.com/understanding-rxjava-subscribeon-and-observeon-744b0c6a41ea
```

### Index
- [Reactor](reactor)
- [RxJava](#)

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

Observable 을 Subscribe 한다

<define>
Observable ob = Observable.from() - hot (eager)
Observable ob = Observable.create() - cold (lazy)
Observable ob = Observable.just()

<operations>
ob.subscribe();

ob.from()
.map()
.filter()
.onNext()
.onCompleted()
.subscribe();

<execution over control>
Subscription sub = ob.subscribe(...);
sub.unsubscribe();


<execution over pooling>
// Subscribe 하는 클라이언트가, 발행된 아이템을 처리할때 사용하는 풀 지정
Subscription sub = ob.subscribeOn(Schedulers.io()).subscribe(...);
// Observable 이, 자신을 구독하는 client 에게 발생할때 사용하는 풀 지정
Observable.observeOn(Schedulers.io()).from(...)


<Observable types>
Single - 1개의 아이템만 발행가능한 Observable (1개의 응답이 있는 메소드)
Completable - 0개의 아이템이 발행가능한 Observable (void 형태의 메소드)
