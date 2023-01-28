## Reactor

```
ㅁ Author: suktae.choi
- https://projectreactor.io/docs/core/release/reference
- https://javacan.tistory.com/search/%EC%8A%A4%ED%94%84%EB%A7%81%20%EB%A6%AC%EC%95%A1%ED%84%B0%20%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0
- https://kazuhira-r.hatenablog.com/entry/20160827/1472291329
```

#### Index

- [reactor operator](reactor-operator)

***

### Core Features

#### Publisher (== `Observable` in RxJava)

- Flux - 0...N 개의 데이터를 가짐

  ```java
  List<? extends T> datas
  ```

- Mono - 0...1 개의 데이터를 가짐

  ```java
  @Nullable T data;
  ```

#### Subscriber (== `Observer` in Rxjava)

- 서비스로직 구현, 데이터를 소비
  - Consumer<? super T>  consumer

#### Subscription

- executionContext 로 이해하면됨
  - Publisher 의 data 참조하고, Subscriber 가 누구인지 알고있음
  - Subscriber 와 실질적으로 통신하며 데이터를 전달
  - beanScope.request 같이 해당 요청단위(== 구독) 에서만 유효함

```java
public interface Publisher<T> {
  /**
   * Request {@link Publisher} to start streaming data.
   */
  public void subscribe(Subscriber<? super T> s);
}

public interface Subscriber<T> {
  /**
   * Invoked after calling {@link Publisher#subscribe(Subscriber)}.
   *
   * No data will start flowing until {@link Subscription#request(long)} is invoked.
   * It is the responsibility of this {@link Subscriber} instance to call {@link Subscription#request(long)} whenever more data is wanted.
   */
  public void onSubscribe(Subscription s);
  public void onNext(T t);
  public void onError(Throwable t);
  public void onComplete();
}

public interface Subscription {
  public void request(long n);
  public void cancel();
}
```

![Screen Shot 2019-02-08 at 02.00.45](images/Screen%20Shot%202019-02-08%20at%2002.00.45.png)

#### Request/Response

- [req] subscription.request(N)
- [res] for (i = offset; i < N; i++) { subscriber.onNext(datas[i]) }

```java
/* request(1) */
[req] subscriber -> subscription
[res] subscriber <- subscription
[req] subscriber -> subscription
[res] subscriber <- subscription
...
    
/* request(N) */
[req] subscriber -> subscription
[res] subscriber <- subscription
[res] subscriber <- subscription
[res] subscriber <- subscription
[res] subscriber <- subscription
... until N times
```

> request(1) 로 요청하면 polling 과 동일하게 동작한다.
>
> 불필요한 round-trip 을 방지하기위해, 내부적으로 request(N) 후 buffer 에 저장해서 1개씩 응답하는 구현체도 있다.

#### Push/Pull

- Pull
  - 구독 (subscribe) 은 완료된 상태
  - (subscriber -> publisher) #request
  - (subscriber <- publisher) #onNext 로 데이터를 받음
- Push
  - 구독 (subscribe) 은 완료된 상태
  - ~~(subscriber -> publisher) #request~~
  - (subscriber <- publisher) #onNext 로 데이터를 받음

#### Backpressure

- [req] subscription.request(N) 에서 N 을 조절함
  - 여유가있으면 - N++
  - 여유가없으면 - N--

```java
.subscribe(new Subscriber<Integer>() {
    private Subscription s;
    private List<Integer> buffer = new ArrayList<>();
	private final int DEFAULT_CHUNK_SIZE = 10;
    private final int MAX_CAPACITY = 100;
 
    @Override
    public void onSubscribe(Subscription s) {
        if (Objects.isNull(this.s)) {
            this.s = s;
        }
        // 최초 요청
        s.request(DEFAULT_CHUNK_SIZE);
    }
 
    @Override
    public void onNext(Integer integer) {
        buffer.add(s);

        // backPressure 
        subscription.request(buffer.size() > MAX_CAPACITY ? DEFAULT_CHUNK_SIZE / 2 : DEFAULT_CHUNK_SIZE);
    }
    
    // ... onError(), onComplete()
});
```

#### Schedulers (== thread pool)

- Schedulers.immediate()

> The current thread

- Schedulers.single()
- Schedulers.newSingle("pool-prefix")

> Executors.newSingleThreadExecutor()
>
> - thread count: fixed (1)
> - queue size: unbounded

- Schedulers.parallel()
- Schedulers.newParallel("pool-prefix", 4) // thread count: 4

> Executors.newFixedThreadPool()
>
> - thread count: fixed (1..N, default count per cores)
> - queue size: unbounded

- Schedulers.elastic()
- Schedulers.newElastic("pool-prefix", 300) // ttl 300s

> Executors.newCachedThreadPool()
>
> - thread count: dynamic (1...unbounded, default ttl 60s)
> - queue size: 0

Reactor 에서 제공하는 blocking APIs (`block()`, `blockFirst()`, `blockLast()`,` toIterable()`, `toStream()`) 는 **elastic** 에서만 지원한다. 다른 Schedulers & Custom pool 은 IlegalStateException 이 발생

> 생각해보면 unbounded-worker 가 생성되는 elastic 는 blocking APIs, bounded type 인 다른 풀은 미지원

newXXX() 를 통해 직접 생성한 쓰레드풀은 application shutdown 시 명시적으로 dispose() 를 호출해서 종료필요

#### publishOn/subscribeOn

- publishOn
  - 선언부분 아래부터 지정된 스케쥴러에서 async 로 수행 (== affected below)
  - Typically used for ``fast publisher``, ``slow consumer(s)`` scenarios.
- subscribeOn
  - 지정된 스케쥴러에서 async 로 수행 (== affected emission)
  - Typically used for ``slow publisher`` (e.g., blocking IO), ``fast consumer(s)`` scenarios.

```java
/* publishOn() */
[main] Application main
[main] subscribe()
[main] onSubscribe()
[main] just, create, generate
[main] ...
	publishOn("PUB1")	// affect below
[PUB1] map
[PUB1] flatMap
[PUB1] filter

/* subscribeOn() */
[main] Application main
[main] subscribe()
[main] onSubscribe()
[SUB1] just, create, generate // blocking IO
[SUB1] ...
[SUB1] map
[SUB1] flatmap
	subscribeOn("SUB1")	// affect emission
	subscribeOn("SUB2") // ignored
[SUB1] filter

/* publishOn() + subscribeOn() */
[main] Application main
[main] subscribe()
[main] onSubscribe()
[SUB1] just, create, generate // blocking IO
[SUB1] ...
	publishOn("PUB1")
[PUB1] map
[PUB1] flatmap
	subscribeOn("SUB1")	// affect emission
	subscribeOn("SUB2") // ignored
[PUB1] filter
```

#### ![Screen Shot 2019-02-10 at 23.48.55](images/Screen%20Shot%202019-02-10%20at%2023.48.55.png)

### Sequence

#### Emission

**정해진 source (ex. Collection) 에서 생성하는 방법**

- just()
- range()
- fromStream()
- fromIterable()

```java
// just
Flux.just(0, 1, 2, 3, 4);

// range
flux.range(0, 4);

// fromStream
Flux.fromStream(Stream.of(0, 1, 2, 3, 4));

// fromIterable
Flux.fromIterable(Arrays.asList(0, 1, 2, 3, 4));
```

**Custom source (ex. 사용자입력) 에서 생성하는 방법**

- generate
  - push - 미지원
    - 요청 - 응답 없음 (async)
    - 요청 없음 - 응답 (async)
    - 요청 1번 - 응답 N번
  - multi-thread  - 미지원
  - state - **지원**

```java
/* Consumer<T> */
// 조건만족 확인못함 (state 가 없음)
Flux.generate(o -> {
	Random random = new Random();
    int ranInt = random.nextInt();
    
    o.next(ranInt);
    
    if (/* 조건만족시 */ true) {
        o.complete();
    }
});

// 조건만족 확인위해, instance field 가 필요함 (state)
Flux.generate(new Consumer<SynchronousSink<Integer>>() {
	private int emitCount = 0;

    @Override
    public void accept(SynchronousSink<Integer> o) {
        Random random = new Random();
        int ranInt = random.nextInt();

        o.next(ranInt);
        emitCount++;

        if (/* 조건만족시 */ emitCount > 10) {
            o.complete();
        }
    }
});

/* Supplier<S>, BiFunction, Consumer<S> with state */
// 조건만족 확인가능 (state 가 있음)
Flux.generate(
    () -> 0, // init
    (state, o) -> {
        Random random = new Random();
        int ranInt = random.nextInt();

        o.next(ranInt);

        if (/* 조건만족시 */ state > 10) {
            o.complete();
        }
        return state++;
    },
    state -> System.out.println("state: " + state) // destroy
);
```

> 요청에 대한 응답을 **동기적으로 (sync)** 하는 케이스에 적합

에러가 발생하는 케이스는

```java
// push 미지원
Flux.generate(o -> {
	Random random = new Random();
    int ranInt = random.nextInt();
    
    o.next(ranInt);
    o.next(ranInt);	// 요청 1번 - 응답 N번
});

java.lang.IllegalStateException: More than one call to onNext
	at reactor.core.publisher.FluxGenerate$GenerateSubscription.next(FluxGenerate.java:157)
	at SequenceTest.lambda$generateTest$0(SequenceTest.java:22)
	at reactor.core.publisher.FluxGenerate.lambda$new$1(FluxGenerate.java:56)
```

```java
// push 미지원
Flux.generate(o -> {
    Listener.addEventListener(new ListenerSupport() {
        @Override
        public void onData(int data) {
            o.next(data);	// 요청 없음 - 응답 (async)
        }
        
        @Override
        public void onComplete() {
            o.complete();
        }
    });
    
    // 요청 - 응답 없음 (async)
});

java.lang.IllegalStateException: The generator didn`t call any of the SynchronousSink method
	at reactor.core.publisher.FluxGenerate$GenerateSubscription.slowPath(FluxGenerate.java:276)
	at reactor.core.publisher.FluxGenerate$GenerateSubscription.request(FluxGenerate.java:204)
	at reactor.core.publisher.FluxPeekFuseable$PeekFuseableSubscriber.request(FluxPeekFuseable.java:138)
	at reactor.core.publisher.BaseSubscriber.request(BaseSubscriber.java:212)
	at ch4.CreateTest$3.hookOnSubscribe(CreateTest.java:86)	
```

- create(FluxSink\<T\>)
  - push - **지원**
    - 요청 - 응답 없음 (async)
    - 요청 없음 - 응답 (async)
    - 요청 1번 - 응답 N번
  - multi-thread  - **지원**
  - state - 없음

```java
/* Consumer<T> */
// push 지원
Flux.create(o -> {
    Listener.addEventListener(new ListenerSupport() {
        @Override
        public void onData(int data) {
            o.next(data); // 요청 없음 - 응답 (async)
            o.next(data); // 요청 1번 - 응답 N번
        }
        
        @Override
        public void onComplete() {
            o.complete();
        }
    });
    
    // 요청 - 응답 없음 (async)
});

/* Consumer<T>, OverflowStrategy */
DROP - 데이터 누락
LATEST - 마지막 신호만 전달
ERROR - exception 발생
IGNORE - 무시하고 신호 발생
:: cause exception on subscriber
BUFFER[default] - (publisher 의) unbounded-buffer 에 저장
:: cause exception (OOM) on publisher
```

> 요청에 대한 응답을 **비동기적으로 (async)** 하는 케이스에 적합

- push (== create 와 유사함)
  - push - **지원**
    - 요청 - 응답 없음 (async)
    - 요청 없음 - 응답 (async)
    - 요청 1번 - 응답 N번
  - multi-thread  - 미지원
    - onNext, onComplete, onError 이벤트를 발행하는 thread 가 동일해야함
  - state - 없음

### Handle

- handle(BiConsumer<T, SynchronousSink>)

  - filter + map
  - but returns `SynchronousSink`, not suitable for #create and/or #push

  ```java
  public class SequenceHandler {
    public Flux<Integer> handle(Flux<Integer> sequence) {
      return sequence.handle((number, sink) -> {
        if (number % 2 == 0) {
          sink.next(number / 2); // SynchronousSink used
        }
      });
    }
  }
  ```

- filter and/or map
  
  - 

### Exception

```java
Flux.just(1, 2, 0)
    .map(i -> "100 / " + i + " = " + (100 / i)) // divide-by-zero
    .subscribe(
	    o -> System.out.println(o),	// onNext
    	o -> System.out.println("errors: " + o)	// onError
);
```

#### error

- onErrorReturn(T value)
  - return given value
- onErrorResume(Function<Throwable, Publisher\<T\>> fallback)
  - start new sequence
- onErrorMap(Function<Throwable, Throwable> mapper)
  - catch & re-throw
- onErrorContinue(BiConsumer<Throwable, Object> consumer)
  - ignore element -> continue current sequence

```java
Flux.just(1, 2, 0, 3)
    .map(i -> 100 / i)
    .onErrorContinue((throwable, o) -> { // ignore element
        System.out.println(throwable);
        System.out.println("error element: " + o);
    })
    .onErrorMap(throwable -> new IllegalArgumentException()) // catch & re-throw
    .onErrorResume(throwable -> Flux.just(5, 10, 50)) // start new sequence
    .onErrorReturn(100) // return given value & exit
    .subscribe(o -> System.out.println(o));
```

#### finally

- doFinally(Consumer\<SignalType\> onFinally)
  - finally
- using
  - try-with-resource

```java
/* onFinally#complete */
Flux.just(1, 2, 3, 4, 5)
    /**
    * 종료이벤트가 발생되면, 바로 호출됨
    */
    .doFinally(exitType -> {
        /**
        * onComplete
        * onError
        * cancel
        */
        System.out.println("onFinally: " + exitType);
    })
    .subscribe(
    o -> System.out.println("onNext: " + o),
    o -> System.out.println("onError: " + o),
    () -> System.out.println("onComplete")
);
// console 결과
onNext: 1
onNext: 2
onNext: 3
onNext: 4
onNext: 5
onComplete
onFinally: onComplete

/* onFinally#complete */
Flux.just(1, 2, 3, 4, 5)
    /**
    * 종료이벤트가 발생되면, 바로 호출됨
    */
    .doFinally(exitType -> {
        /**
        * onComplete
        * onError
        * cancel
        */
        System.out.println("onFinally: " + exitType);
    })
    .take(1) // 1개 element 처리후, subscription#cancel 이벤트 발생
    .subscribe(
    o -> System.out.println("onNext: " + o),
    o -> System.out.println("onError: " + o),
    () -> System.out.println("onComplete")
);
// console 결과
onNext: 1
onFinally: cancel
onComplete
```

```java
/* using */
Flux.using(
    () -> Files.lines(Paths.get("owfs_file")), // read file
    o -> {	// string to Flux
        System.out.println(o);
        return Flux.fromStream(o);
    },
    o -> { // finally
        System.out.println("onFinally");
        o.close();
    }).subscribe(System.out::println);
```

#### retry

- retry(long try)
  - 무조건 재시도, 재시도 횟수 지정
- retryWhen(Function<Throwable, Publisher\<T\>>)
  - 에러가 발생할 때마다 에러가 companion Flux로 전달된다.
  - companion Flux가 뭐든 발생하면 재시도가 일어난다.
  - companion Flux가 종료되면 재시도를 하지 않고 원본 시퀀스 역시 종료된다.
  - companion Flux가 에러를 발생하면 재시도를 하지 않고, 컴페니언 Flux가 발생한 에러를 전파한다.

```java
/* retry */
Flux.just(1, 2, 3)
    .log()
    .map(i -> {
        if (i < 3) {
            return i;
        } else {
            throw new IllegalStateException();
        }
    })
    .retry(1) // so
    .subscribe(
	o -> System.out.println("onNext: " + o),
    err -> System.err.println("onError: " + err),
    () -> System.out.println("onComplete")
);
// console 결과
onNext: 1
onNext: 2
  -- exception
onNext: 1
onNext: 2
IllegalStateException

/* retryWhen */
Flux.just(1, 2, 3)
    .log()
    .map(i -> {
        if (i < 3) {
            return i;
        } else {
            throw new IllegalStateException();
        }
    })
    .retryWhen(errorsFlux -> errorsFlux
    	.take(1))
    .subscribe(
		o -> System.out.println("onNext: " + o),
    	err -> System.err.println("onError: " + err),
	    () -> System.out.println("onComplete")
);
// console 결과
onNext: 1
onNext: 2
  -- exception
onNext: 1
onNext: 2
onComplete
```

#### re-throw

Sequence 이벤트 발생시, try~catch 에서 잡힌 Exception 은 re-throw 가 안된다. 

```java
// exception throw 하는 method
private String convert(int i) throws IOException {
    throw new IOException();
}

public Flux<String> convertAsync() throws IOException { // throws IOException 선언 필요
    try {
        return convert(3);
    } catch (Exception e) {
        throw e;
    }
}
```

다른 예외로 convert 해서 우회가능하나, re-throw 하고싶으면 해당 예외를 wrapping 하는 유틸이 있다.

- Exceptions.propagate(Throwable t)
  - re-throw 된 예외를 wrap
- Exceptions.unwrap(Throwable t)
  - re-throw 된 예외를 unwrap

```java
// re-throw 가 안된다
public Flux<String> convertAsync() throws IOException {
    Flux.range(1, 10)
    .map(o -> {
        try {
            return convert(o);
        } catch (Exception e) {
            throw e; // unhandled exception: IOException
            // throw new IllegalArgumentException(); 로 바꿔서 던지는건 가능
        }
    });
}
```

```java
// wrap(re-throw) 해서 전파가능
Flux.range(1, 10)
    .map(i -> {
        try {
            return convert(i);
        } catch (IOException e) {
            throw Exceptions.propagate(e); // wrap & re-throw
        }
    })
    .subscribe(
    v -> System.out.println("onNext"),
    e -> {
        Throwable throwable = Exceptions.unwrap(e); // unwrap

        if (throwable instanceof IOException) {
            System.out.println("onError: IOException");
        } else {
            System.out.println("onError: Exception");
        }
    }
);
// console 결과
onError: IOException
```

### Processors

Publisher and/or Subscriber 을 모두 만족하는 Bridge

```java
/* Processor */
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
}
```

#### Direct

broadcasting without circular-buffer

- DirectProcessor
  - backPressure - 미지원
  - subscriber - N개
- UnicastProcessor
  - backPressure - 지원
  - subscriber - 1개

```java
/* DirectProcessor */
@Test
public void directProcessorTest() {
    // create processor
    DirectProcessor<Integer> processor = DirectProcessor.create();
    // create publisher
    FluxSink<Integer> fluxSink = processor.sink();
    processor.subscribe(o -> System.out.println("[1]=" + o));
    
    fluxSink.next(1); // processor 에서 파생된 publisher 에서 발행하거나
    processor.onNext(2); // processor 에서 직접 발행할수도있고
    
    processor.subscribe(o -> System.out.println("[2]=" + o));

    fluxSink.next(3);
    fluxSink.next(4);
}
// console 결과
[1]=1
[1]=2
[1]=3
[2]=3
[1]=4
[2]=4
```

```java
/* UnicastProcessor */
@Test
public void unicastProcessorTest() {
    // create processor
    UnicastProcessor<Integer> processor = UnicastProcessor.create(); // queue 전달가능, default unbounded-queue
    // create publisher
    FluxSink<Integer> fluxSink = processor.sink();
    
    fluxSink.next(1);
    processor.onNext(2);
    
    processor.subscribe(o -> System.out.println("[1]=" + o));
	// processor.subscribe(o -> System.out.println("[2]=" + o)); // exception

    fluxSink.next(3);
    fluxSink.next(4);
}
// console 결과
[1]=1 -- rewind 과정
[1]=2 -- rewind 과정
[1]=3
[1]=4
    
// console 결과 (exception)
Caused by: java.lang.IllegalStateException: UnicastProcessor allows only a single Subscriber
	at reactor.core.publisher.UnicastProcessor.subscribe(UnicastProcessor.java:430)
	at reactor.core.publisher.Flux.subscribe(Flux.java:7743)
	at reactor.core.publisher.Flux.subscribeWith(Flux.java:7907)
```

#### Synchronous

looping internally (circular buffer), processing synchronously

- EmitterProcessor
  - backPressure - 지원
  - subscriber - N개
  - no cached
- ReplayProcessor
  - backPressure - 지원
  - subscriber - N개
  - cached (이전에 발행된 데이터를 캐싱)

```java
/* EmitterProcessor */
@Test
public void emitterProcessorTest() {
    // create processor
    EmitterProcessor<Integer> processor = EmitterProcessor.create();
    // create publisher
    FluxSink<Integer> fluxSink = processor.sink();

    processor.subscribe(o -> System.out.println("[1]=" + o));

    fluxSink.next(1);
    processor.onNext(2);

    processor.subscribe(o -> System.out.println("[2]=" + o));

    fluxSink.next(3);
    fluxSink.next(4);
}
// console 결과
[1]=1
[1]=2
[1]=3
[2]=3
[1]=4
[2]=4

/* ReplayProcessor */
동일한 코드로 ReplayProcessor 를 만든다면
// console 결과
[1]=1
[1]=2
[2]=1 -- rewind 과정
[2]=2 -- rewind 과정
[1]=3
[2]=3
[1]=4
[2]=4
```

#### Asynchronous

looping internally (circular buffer), processing asynchronously

- TopicProcessor
  - backPressure - 지원
  - subscriber - N개
  - no cached
  - async
- WorkQueueProcessor
  - backPressure - 지원
  - subscriber - N개 (1개의 subscriber 만 이벤트를 받음)
  - no cached
  - async

```java
/* TopicProcessor: sync */
@Test
public void topicProcessorTest() {
    // create processor
    TopicProcessor<Integer> processor = TopicProcessor.create(); // create 로 생성하면 sync
    // create publisher
    FluxSink<Integer> fluxSink = processor.sink();

    processor.subscribe(o -> System.out.println("[1]=" + o));

    fluxSink.next(1);
	processor.onNext(2);

    processor.subscribe(o -> System.out.println("[2]=" + o));

    fluxSink.next(3);
    fluxSink.next(4);
}
// console 결과
[1]=1
[1]=2
[1]=3 -- sync
[2]=3 -- sync
[1]=4 -- sync
[2]=4 -- sync
    
/* TopicProcessor: async */
@Test
public void topicProcessorAsyncTest() {
    // create processor
    TopicProcessor<Integer> processor = TopicProcessor.share("ASYNC-", 256); // thread-pool 지정 및 TopicProcessor#share 로 생성
    // create publisher
    FluxSink<Integer> fluxSink = processor.sink();

    processor.subscribe(o -> System.out.println("[1]=" + o));

    fluxSink.next(1);
	processor.onNext(2);

    processor.subscribe(o -> System.out.println("[2]=" + o));

    fluxSink.next(3);
    fluxSink.next(4);
}
// console 결과
[1]=1
[1]=2
[1]=3 -- async
[1]=4 -- async
[2]=3 -- async
[2]=4 -- async
```

```java
/* WorkQueueProcessor */
동일한 코드작성

// console 결과
[1]=1
[1]=2
[1]=3 -- one of subscriber
[2]=4 -- one of subscriber
```

### Test

#### StepVerifier

테스트할 대상인 Publisher 를 전달하고, onNext/onError/onComplete 가 발생시 예상되는 return 값을 검증하는 코드를 작성한다.

- StepVerifier.create(Publisher\<T\>)

Emission 되는 각각의 element 에 대해 assert 진행후, 마지막에 verify() 을 호출하여 (lazy-evaluation) 작성된 TC 를 수행한다.

- expectNext(T)
- expectComplete()
- expectError()
- verify()

```java
// 기본적인 emission test
StepVerifier.create(Flux.just("foo", "bar"))
  .expectNext("foo")	// onNext
  .expectNext("bar")	// onNext
  .expectComplete()	// onComplete
  .verify();
```

Exception 이 예상되는 케이스는 다음과 같이 처리한다.

```java
// @Test(expected = Class<? extends Throwable>) 와 동일한 역할
assertThatExceptionOfType(AssertionError.class)
  .isThrownBy(() -> 
		StepVerifier.create(...)
		.verify())
  .withMessage("AssertionError occur");
```

부가적인 로깅

- as(String)

```java
private <T> Flux<T> appendError(Flux<T> source) {
  return source.concatWith(Mono.error(new IllegalArgumentException("errorMsg")));
}

/* as(String) */
@Test
public void testAppendError() {
  Flux<String> source = Flux.just("foo", "bar");

  Duration duration = StepVerifier.create(appendBoomError(source))
    .expectNext("fuu")
    .as("첫번째 expectNext() 실패")	// expectNext("fuu") 에서 실패시 detailMessage 출력
    .expectNext("bar")
    .expectErrorMessage("errorMsg")
    .verify();
}
```

#### Manipulate Time

StepVerifier can be used in time-based scenario.

- StepVerifier.withVirtualTime(Supplier<? extends Publisher>)

```java
// Publisher is delayed in specified duration
StepVerifier.withVirtualTime(() -> Mono.delay(Duration.ofDays(1)).map(o -> "foo"))
	// handling time
```

There are two expectation methods that deal with time

- thenAwait(Duration) - 지정된 Duration 이후, 다음 event 의 test 수행
- expectNoEvent(Duration) - 지정된 Duration 이후, event 가 발생하지 않음을 test

```java
/* thenAwait(Duration) */
StepVerifier.withVirtualTime(() -> Mono.delay(Duration.ofDays(1)).map(o -> "foo"))
  .thenAwait(Duration.ofDays(2))
  .expectNext("foo")
  .expectComplete()
  .verify();
```

```java
/* expectNoEvent() */
StepVerifier.withVirtualTime(() -> Mono.delay(Duration.ofDays(1)))
  .expectSubscription() 
  .expectNoEvent(Duration.ofDays(1)) 
  .expectNext(0L) 
  .verifyComplete(); 
```

> `expectNoEvent` also considers the `subscription` as an event. If you use it as a first step, it usually fails because the subscription signal is detected. Use`expectSubscription().expectNoEvent(duration)` pattern instead.

#### Context

Reactor 는 기본적으로 async + parallel (선택적) 이므로 ThreadLocal 로 임시데이터를 관리하기 어렵다.

그리고 stream-scoped 의 변수를 저장하기에 적합하지 않으므로 (ThreadLocal 은 thread-scoped) Reactor 3.1 부터 Context 를 지원한다. 더 자세한 내용은 Advanced Features 에 정리하고, StepVerifier 로 Context 를 테스트하는 방법은 크게 2가지가 있다.

- expectAccessibleContext() - context 가 있음을 검증
- expectNoAccessibleContext() - context 가 없음을 검증

```java
/* expectAccessibleContext() */
StepVerifier.create(Mono.just(1), StepVerifierOptions.create().withInitialContext(Context.of("foo", "bar")))
  .expectAccessibleContext()	// assert context is not null
  .contains("foo", "bar")	// assert context contains
  .then()	// put back to test
  .expectNext(1)
  .verifyComplete();
```

```java
/* expectNoAccessibleContext() */
StepVerifier.create(Mono.just(1))
  .expectNoAccessibleContext()	// assert context is null
  .expectNext(1)
  .verifyComplete();
```

#### TestPublisher

실제 Publisher 를 사용하는게 어렵다면, TestPublisher 를 만들어서 이벤트 발행을 할수있다.

- next(T)
- complete()
- error(Throwable)
- emit(T…) - next(T) + complete()

```java
/* TestPublisher.create() */
@Test
public void testPublisher() {
  TestPublisher publisher = TestPublisher.create()
    .next("A")
    .complete();

  StepVerifier.create(publisher)
    .expectNext("A")
    .expectError()
    .verify();
}
```

> Flux.just("A") vs TestPublisher.create().next("A") 는 테스트 관점으로 보면 동일하지 않을까?

의도적으로 예외상황 (ex. stackOverFlow 등) 을 만들수있다.

```java
/* TestPublisher.createNoncompliant(Enum.Violation) */
@Test
public void testPublisher() {
  TestPublisher publisher = TestPublisher
    .createNoncompliant(TestPublisher.Violation.REQUEST_OVERFLOW);

  StepVerifier.create(publisher)
    .expectNext("A")
    .expectError()
    .verify();
}

/* Enum.Violation */
REQUEST_OVERFLOW - continuous onNext
ALLOW_NULL - onNext(null)
CLEANUP_ON_TERMINATE - onComplete + onError
DEFER_CANCELLATION - ignore cancel signal
```

TestPublisher 도 Publisher 를 상속하여, 직접 사용할 수 있지만 Flux/Mono conversion 도 지원한다

```java
/* TestPublisher -> Flux/Mono */
@Test
public void testPublisher() {
  TestPublisher publisher = TestPublisher.create()
    .next("A")
    .complete();

  StepVerifier.create(publisher.flux()) // or .mono()
    .expectNext("A")
    .expectError()
    .verify();
}
```

#### PublisherProbe

Before version 3.1, you would need to manually maintain one `AtomicBoolean` per state you wanted to assert and attach a corresponding `doOn*` callback to the publisher you wanted to evaluate. This could be a lot of boilerplate when having to apply this pattern regularly. Fortunately, since 3.1.0 there’s an alternative with `PublisherProbe`, as follows

### Debugging

기본적으로 Reactive Stream 은 디버깅이 어렵습니다. 성공/실패시 바로 그 지점에서 Exception 이 떨어지지않고 해당 이벤트에 따른 처리가 Stream 으로 흘러가기 때문입니다. 그래서 별도의 breakpoint 를 잡으며 따라가지 않으면 쉽게 파악이 되지 않습니다.

> 만약 스트림을 비동기로 처리했다면?! HER..

Reactor 에서는 프로그램 상에서 easy-debugging 을 위한 3가지 방법을 제공합니다.

**Hooks**

- JVM level 에서의 global debug_mode 설정 (jvm-scoped)

```java
/* Hooks */
@Test
public void testHooks() {
  	Hooks.onOperatorDebug();    // enable debug_mode

	  Mono<Integer> mono = Flux.range(1, 5)
    	.map((num) -> num * 10)
    	.single();

	  mono.subscribe(System.out::println);
  	Hooks.resetOnOperatorDebug();   // disable debug_mode
}
```

**checkpoint(String)**

- chain 에서 예외발생시, downstream 에 checkpoint() 가 있다면 debug-message 출력 (downstream-scoped)

```java
/* checkpoint(String) */
@Test
public void testCheckPoint() {
Flux<Integer> flux = Flux.just(10, 20, 50, 21, 0, 15, 62)
  .checkpoint("just() 예외발생")
  .map(num -> num * 10)
  .checkpoint("num -> num * 100 에서 예외발생")
  .map(num -> 100 / num)	// java.lang.ArithmeticException: / by zero
  .checkpoint("num -> 100 / num 에서 예외발생")	// description is given
  .map(num -> num + 10)
  .checkpoint("num -> num + 10 에서 예외발생");

  flux.subscribe(System.out::println);
}
// console
Suppressed: reactor.core.publisher.FluxOnAssembly$OnAssemblyException: 
Assembly site of producer [reactor.core.publisher.FluxMapFuseable] is identified by light checkpoint [num -> 100 / num 에서 예외발생].

/* checkpoint(String) - 예외발생시 아래에 있는 checkpoint() 가 검색됨 */
@Test
public void testCheckPoint() {
Flux<Integer> flux = Flux.just(10, 20, 50, 21, 0, 15, 62)
  .checkpoint("just() 예외발생")
  .map(num -> num * 10)
  .checkpoint("num -> num * 100 에서 예외발생")
  .map(num -> 100 / num)	// java.lang.ArithmeticException: / by zero
  //.checkpoint("num -> 100 / num 에서 예외발생")
  .map(num -> num + 10)
  .checkpoint("num -> num + 10 에서 예외발생");	// description is given

  flux.subscribe(System.out::println);
}
// console
...
Suppressed: reactor.core.publisher.FluxOnAssembly$OnAssemblyException: 
Assembly site of producer [reactor.core.publisher.FluxMapFuseable] is identified by light checkpoint [num -> num + 10 에서 예외발생].
```

**log()**

- chain 에서 예외발생시, debug-message 출력 (chain-scoped)

```java
/* log() */
@Test
public void testLog() {
  Flux<Integer> flux = Flux.just(10, 20, 50, 21, 0, 15, 62)
    .map(num -> num * 10)
    .map(num -> 100 / num)	// java.lang.ArithmeticException: / by zero
    .map(num -> 10 + num)
    .log();

  flux.subscribe(System.out::println);
}
```

### Advanced Features

#### How Do I Wrap a Synchronous, Blocking Call?

fromRunnable or fromCallable 로 감싼다.

```java
Mono<Boolean> files = Mono.fromCallable(() -> {
  log.info("Mono1={}", Thread.currentThread().getName());
  Path path = Paths.get("classpath:test.yaml");

  return Files.deleteIfExists(path);
}).subscribeOn(Schedulers.boundedElastic());

Boolean result = files.blockOptional().orElse(null);
```

You should use `Schedulers.boundedElastic`, because it creates a dedicated thread to wait for the blocking resource without impacting other non-blocking processing, while also ensuring that there is a limit to the amount of threads that can be created, and blocking tasks that can be enqueued and deferred during a spike.

#### Mutualizing Operator

반복되는 연산을 function<T, V> 으로 묶어서 재사용 할수있으며, 아래의 예제는 동일한 결과가 나온다:

```java
@Test
public void transformTest() {
  Function<Flux<String>, Flux<String>> filterAndMap = f -> f
    .filter(color -> !color.equals("orange"))	// filter
    .map(String::toUpperCase);	// map

  Flux.fromIterable(Arrays.asList("blue", "green", "orange", "purple"))
    .doOnNext(System.out::println)
    .transform(filterAndMap)
    .subscribe(d -> System.out.println("Subscriber to Transformed MapAndFilter: " + d));
}

/* equivalent to .. */
@Test
public void transformEquivalentTest() {
  Flux.fromIterable(Arrays.asList("blue", "green", "orange", "purple"))
    .doOnNext(System.out::println)
    .filter(color -> !color.equals("orange"))	// filter
    .map(String::toUpperCase)	// map
    .subscribe(d -> System.out.println("Subscriber to Transformed MapAndFilter: " + d));
}
```

Reactor 에서는 2가지 wrapper 를 지원한다.

**transform(Function<? super Flux\<T\>, ? extends Publisher\<V\>>)**

Subscriber 가 subsribe 하기전에 eager-loaded

```java
/* transform() */
@Test
public void composeTest() {
  AtomicInteger count = new AtomicInteger(0);
  Function<Flux<Integer>, Flux<Integer>> mapper = f -> {
    count.incrementAndGet();

    return f;
  };

  Flux<Integer> flux = Flux.just(0)
    .transform(mapper);

  System.out.println(count);

  flux.subscribe();
  flux.subscribe();
  flux.subscribe();

  System.out.println(count);
}
// console 결과
1
1
```

**compose(Function<? super Flux\<T\>, ? extends Publisher\<V\>>)**

Subscriber 가 subsribe 하는시점에 lazy-loaded

```java
/* javadoc */
public final <V> Flux<V> compose(Function<Flux<T>, ? extends Publisher<V>> transformer) {
   return defer(() -> transformer.apply(this));	// defer
}

/////////////////////////////////////////////////////////////////////////////

/* compose() */
@Test
public void composeTest() {
	// ...

  Flux<Integer> flux = Flux.just(0)
    .compose(transformer);

  // ...
}
// console 결과
0
3
```

#### Hot/Cold

- Cold publisher
  - subscriber 가 #subscribe() 할때, 데이터 발행
- Hot publisher
  - publisher 정의시점에 데이터 발행되고 & 흘러감
  - subscriber 가 #subscribe() 하면, 그떄시점 부터 발행되는 element 를 받을수 있음

> Most of `hot publishers` in Reactor extend **Processor**.

```java
/* cold */
@Test
public void coldPublisherTest() {
  Flux<Integer> coldFlux = Flux.fromIterable(Arrays.asList(0, 1, 2, 3));

  coldFlux.subscribe(d -> System.out.println("[0]: " + d));
  coldFlux.subscribe(d -> System.out.println("[1]: " + d));
}
// console 결과
[0]: 0
[0]: 1
[0]: 2
[0]: 3
[1]: 0
[1]: 1
[1]: 2
[1]: 3
```

```java
/* hot */
@Test
public void hotPublisherTest() {
  DirectProcessor<Integer> hotSource = DirectProcessor.create();
  Flux<Integer> hotFlux = hotSource.map(Function.identity());

  hotSource.onNext(0);	// missing, no subscriber
  hotFlux.subscribe(d -> System.out.println("[0]: " + d));

  hotSource.onNext(1);
  hotFlux.subscribe(d -> System.out.println("[1]: " + d));

  hotSource.onNext(2);
  hotSource.onNext(3);

  hotSource.onComplete();
}
// console 결과
[0]: 1
[0]: 2
[1]: 2
[0]: 3
[1]: 3
```

#### ConnectableFlux

데이터 발행의 단순 lazy-evaluation (deferred) 이 아닌, 특정시점에 트리거 하도록 제어하는 Publisher

>Not only **defer** some processing to the subscription time of one subscriber, but you might actually want for several of them to *rendezvous* and **then** trigger.

2가지 방법으로 생성 할 수 있다.

**publish**

- 구독시점에 subscriber 로 데이터가 흘러가지 않음
- connect() 로 trigger 시, 그 시점부터 이벤트 발행

```java
/* ConnectableFlux = flux.publish() */
@Test
public void connectableFluxTest_publish() {
  Flux<Integer> flux = Flux.range(1, 3);
  ConnectableFlux<Integer> connectableFlux = flux.publish();

  connectableFlux.subscribe(o -> System.out.println("[0]: " + o));
  connectableFlux.subscribe(o -> System.out.println("[1]: " + o));

  System.out.println("will now connect");
  connectableFlux.connect();  // trigger

  connectableFlux.subscribe(o -> System.out.println("[2]: " + o));    // ignored
}
// console 결과
will now connect
[0]: 1
[1]: 1
[0]: 2
[1]: 2
[0]: 3
[1]: 3
```

**replay**

- 구독시점에 subscriber 로 데이터가 흘러가지 않음
- connect() 로 trigger 시, 그 시점부터 이벤트 발행
- trigger 이후 subscribe 은 기존 데이터 replay (history 를 저장할 bufferSize 지정가능)

```java
/* ConnectableFlux = flux.replay() */
@Test
public void connectableFluxTest_replay() {
  Flux<Integer> flux = Flux.range(1, 3);
  ConnectableFlux<Integer> connectableFlux = flux.replay();

  connectableFlux.subscribe(o -> System.out.println("[0]: " + o));
  connectableFlux.subscribe(o -> System.out.println("[1]: " + o));

  System.out.println("will now connect");
  connectableFlux.connect();

  connectableFlux.subscribe(o -> System.out.println("[2]: " + o));    // replayed
}
// console 결과
will now connect
[0]: 1
[1]: 1
[0]: 2
[1]: 2
[0]: 3
[1]: 3
[2]: 1	// replayed
[2]: 2	// replayed
[2]: 3	// replayed
```

ConnectableFlux 는 이벤트 trigger 하는 4가지 방법을 제공한다.

- connect() - 호출시 triggering
- autoConnect(int) - 지정한 count 도달시 triggering
- refCount(int) - autoConnect && referenceCount == 0 이 되면 data publish 를 pend 하는 기능이 있다

```java
/* connectableFlux.autoConnect() */
@Test
public void connectableFluxTest_autoConnect() throws InterruptedException {
  Flux<Integer> flux = Flux.range(1, 3)
    .publish()
    .autoConnect(2);

  flux.subscribe(o -> System.out.println("[0]: " + o));
  flux.subscribe(o -> System.out.println("[1]: " + o));

  System.out.println("will now connect");

  flux.subscribe(o -> System.out.println("[2]: " + o));
}
// console 결과
-- autoConnect(1)
[0]: 1
[0]: 2
[0]: 3
will now connect

-- autoConnect(2)
[0]: 1
[1]: 1
[0]: 2
[1]: 2
[0]: 3
[1]: 3
will now connect

-- autoConnect(3)
will now connect
[0]: 1
[1]: 1
[2]: 1
[0]: 2
[1]: 2
[2]: 2
[0]: 3
[1]: 3
[2]: 3
```

#### Batches

Flux 에 element 가 많을때 (ex. batch 작업등) 작업을 쪼개서 수행할수 있도록 Partitioning 이 필요하다. Reactor 에서는 3가지 방법을 제공한다.

**Flux<GroupedFlux\<T\>\>**

groupBy 를 이용해 key-value pair 로 partitioning 한다.

```java
/* groupBy */
@Test
public void groupedFluxTest() {
  Flux<String> mapFlux = Flux.just(3, 1, 5, 2, 4, 6, 13, 12, 11)
    .groupBy(i -> i % 2 == 0 ? "even" : "odd") // Flux<GroupedFlux<String, Integer>
    .concatMap(e -> e.map(o -> String.format("[%s]=%s", e.key(), o))); // Flux<String>

  mapFlux.subscribe(o -> System.out.println(o));
}
// console 결과
-- flatMap
[odd]=3
[odd]=1
[odd]=5
[even]=2
[even]=4
[even]=6
[odd]=13
[even]=12
[odd]=11
  
-- flatMapSequential
[odd]=3
[odd]=1
[odd]=5
[odd]=13
[odd]=11
[even]=2
[even]=4
[even]=6
[even]=12

-- concatMap
[odd]=3
[odd]=1
[odd]=5
[odd]=13
[odd]=11
[even]=2
[even]=4
[even]=6
[even]=12
```

- flatMap
  - Ordering - source's order
- flatMapSequential
  - Ordering - group's order
  - Concurrency - 먼저 전달된 GroupedFlux 가 다 처리된후 다음 Flux 의 flatten 수행
- concatMap
  - Ordering - group's order
  - Concurrency - 처리되는대로 flatten 수행 (== parallelized)

**Flux<Flux\<T\>>**

window 를 이용해 Flux-subset 을 만들어 partitioning 한다.

```java
/* window */
@Test
public void windowTest() {
  StepVerifier.create(
    Flux.range(1, 10)
    .window(5, 3) // Flux<Flux<T>>
    .concatMap(Function.identity())) // Flux<T>
    .expectNext(1, 2, 3, 4, 5)
    .expectNext(4, 5, 6, 7, 8)
    .expectNext(7, 8, 9, 10)
    .expectNext(10)
    .verifyComplete();
}
```

**Flux<List\<T\>>**

buffer 를 이용해 Collection\<T\> 를 만들어 partitioning 한다.

```java
/* buffer */
@Test
public void bufferTest() {
  StepVerifier.create(
    Flux.range(1, 10)
    .buffer(5, 3)) // Flux<List<T>>
    .expectNext(Arrays.asList(1, 2, 3, 4, 5))
    .expectNext(Arrays.asList(4, 5, 6, 7, 8))
    .expectNext(Arrays.asList(7, 8, 9, 10))
    .expectNext(Arrays.asList(10))
    .verifyComplete();
}
```

#### ParallelFlux

Serial 하게 정의된 Publisher 를 parallel - runOn 패턴을 이용해서 병렬처리 할 수 있다. 

- parallel(int)
  - data 를 지정된 rails (== chunk) 단위로 분할한다. (default: the number of CPU-cores)
  - 병렬처리를 하진 않는다. 반드시 runOn 을 통해 스케쥴러 명시가 필요함
- runOn(Scheduler)
  - rails 단위로 분리된 task 를 실행할 thread-pool 을 지정한다.

```java
/* parallel - runOn */
@Test
public void parallelTest() {
  Flux.range(0, 16)
    .parallel(4) // parallel level
    .runOn(Schedulers.parallel()) // thread-pool
    .subscribe(i -> System.out.println("[" + Thread.currentThread().getName() + "] " + i));
}
// console 결과
[parallel-2] 1
[parallel-3] 2
[parallel-4] 3
[parallel-1] 0
[parallel-2] 5
[parallel-3] 6
[parallel-1] 4
[parallel-4] 7
[parallel-1] 8
[parallel-2] 9
[parallel-3] 10
[parallel-4] 11
[parallel-1] 12
[parallel-2] 13
[parallel-3] 14
[parallel-4] 15
```

> 기존 publishOn 과 다른점은, parallelism 의 지정이 가능하다는 점이다.

ParallelFlux 다시 Flux 로 변경하려면, ParallelFlux#sequential 를 호출하면 된다. Flux\<T\> 로 변경시 rails 로 분산된 data 는 1개로 합쳐진다.

> rails 가 묶이지만, runOn(Scheduler) 에서 지정된 쓰레드풀에서 실행된다. 즉 병렬로 실행된다.

```java
/* sequential */
@Test
public void parallelTest() {
  Flux.range(0, 100)
    .parallel(4) // parallel level (4-rails)
    .runOn(Schedulers.parallel()) // thread-pool
    .sequential() // single Flux (no-rails)
    .map(o -> o++) // rails 구분없이 수행됨
    .subscribe(i -> System.out.println("[" + Thread.currentThread().getName() + "] " + i));
}
// console 결과
[parallel-1] 4
[parallel-3] 6
[parallel-3] 7
[parallel-1] 8
[parallel-2] 9
[parallel-3] 10
[parallel-4] 11
```

그렇다면 rails vs non-rails 의 차이는 무엇일까?

element 가 rails (== group) 에 속하면, 해당 군은 동일한 Thread 에서 수행됨이 [보장된다](https://javacan.tistory.com/entry/Reactor-Start-7-Parallel). 다른 rails 의 작업이 먼저 끝났더라도 workSteal 이 발생하지않고, 쓰레드가 할당안된 다른 rail 를 찾아서 처리되기 때문이다.

#### Schedulers

Reactor 에서 기본적으로 제공하는 Scheduler 를 customize 하는 방법을 소개한다.

> 내부적으로 병렬처리 되는 operation 을 scheduler 지정없이 사용하면 parallel 스케쥴러로 동작한다.

아래 2가지 방법으로 변경이 가능하다.

**Schedulers.Factory**

생성되는 모든 Scheduler 를 override 해서 customize 한다.

```java
/* Factory (== @Override) */
@Test
public void setFactoryTest() {
  // given
  AtomicInteger count = new AtomicInteger(0);
  Schedulers.setFactory(new Schedulers.Factory() {
    @Override
    public Scheduler newElastic(int ttlSeconds, ThreadFactory threadFactory) {
      count.incrementAndGet();

      return Schedulers.newParallel("custom", ttlSeconds);
    }
  });

  // when
  Schedulers.elastic().dispose();	// invoked
  Schedulers.newElastic("newElastic").dispose();	// invoked

  // then
  Assert.assertEquals(count.get(), 2);
}
```

**Schedulers.decorateExecutorService**

Scheduler 가 return 될때 decorator-chain 을 apply 하여 customize 한다.

```java
/* Decorator (== Proxy) */
@Test
public void addDecoratorTest() {
  // given
  AtomicInteger count = new AtomicInteger(0);
  BiFunction<Scheduler, ScheduledExecutorService, ScheduledExecutorService> decorator = (scheduler, server) -> {
    count.incrementAndGet();

    return server;
  };

  // when
  Schedulers.addExecutorServiceDecorator("decorator1", decorator);
  Schedulers.addExecutorServiceDecorator("decorator2", decorator);
  Schedulers.addExecutorServiceDecorator("decorator3", decorator);

  Schedulers.newSingle("newSingle").dispose();	// invokes all decorators

  // then
  Assert.assertEquals(count.get(), 3);
}
```

#### [Hooks](https://github.com/reactor/reactor-core/blob/master/reactor-core/src/test/java/reactor/core/publisher/HooksTest.java)

**Dropping Hooks**

onNext or onError 가 누락되었을때 기본동작은 `logLevel.debug` 출력이다.

해당 action 을 Hooks 을 통해 제어할 수 있다.

```java
// reset after
@After
public void resetAllHooks() {
  Hooks.resetOnNextDropped();
  Hooks.resetOnErrorDropped();
  Hooks.resetOnOperatorError();
}
/* dropHooks */
@Test
public void errorHooks() throws Exception {
  Hooks.onNextDropped(d -> {
    throw new TestException(d.toString());
  });

  Hooks.onErrorDropped(e -> {
    throw new TestException(e.toString());
  });

  try {
    Operators.onNextDropped("helloooo", Context.empty());
    // Assert.fail();
  } catch (Exception e) {
    System.out.println(e.getStackTrace());
  }

  try {
    Operators.onErrorDropped(new TestException("woooooorld"), Context.empty());
    // Assert.fail();
  } catch (Exception e) {
    System.out.println(e.getStackTrace());
  }
}
// console 결과
helloooo
woooooorld
```

**Internal-Error Hooks**

onNext and/or onError and/or onComplete 수행도중 에러 발생시, Hooks 을 통해 제어 가능하다.

```java
/* errorHooks */
@Test
public void errorHooks() {
  // Hooks onError
  Hooks.onOperatorError((ex, obj) -> {
    log.warn("Hooks. ex={}, obj={}", ex, obj);

    return ex;
  });

  final int value = 10;

  Flux<Integer> flux = Flux.just(1, 2, 0, 3, 4, 5);
  flux.map(o -> value / o)
    .doOnError(c -> log.warn("doOnError. ex={}", c.toString())) // stream onError
    .subscribe(r -> log.info("{} -> {}", value, r));
}
// console 결과
14:54:08.474 [main] INFO  ch8.HooksTest - 10 -> 10
14:54:08.475 [main] INFO  ch8.HooksTest - 10 -> 5
14:54:08.478 [main] WARN  ch8.HooksTest - Hooks. ex=java.lang.ArithmeticException: / by zero, obj=0
14:54:08.479 [main] WARN  ch8.HooksTest - doOnError. ex=java.lang.ArithmeticException: / by zero
```

**Assembly Hooks**

stream-chain 에서 각 operator 가 수행할때, Hooks 을 통해 제어 가능하다.

```java
/* assembleHooks */
@Test
public void assembleOnLastHooks() {
  Hooks.onEachOperator(p -> {
    log.info("onEachOperator. publish={}", p);

    return p;
  });

  Hooks.onLastOperator(p -> {
    log.info("onLastOperator. publish={}", p);

    return p;
  });

  int value = 10;

  Flux<Integer> flux = Flux.just(1, 2, 3, 4, 5);
  flux.map(o -> value / o)
    .subscribe(r -> log.info("{} -> {}", value, r));
}
// console 결과
15:27:10.641 [main] INFO  ch8.HooksTest - onEachOperator. publish=FluxArray
15:27:10.643 [main] INFO  ch8.HooksTest - onEachOperator. publish=FluxMapFuseable
15:27:10.645 [main] INFO  ch8.HooksTest - onLastOperator. publish=FluxMapFuseable
15:27:10.650 [main] INFO  ch8.HooksTest - 10 -> 10
15:27:10.650 [main] INFO  ch8.HooksTest - 10 -> 5
15:27:10.650 [main] INFO  ch8.HooksTest - 10 -> 3
15:27:10.650 [main] INFO  ch8.HooksTest - 10 -> 2
15:27:10.650 [main] INFO  ch8.HooksTest - 10 -> 2
```

**Hook Presets**

몇몇 유용한 방식으로 boot 에 적용.

[ApplicaionListener](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-spring-application.html#boot-features-application-events-and-listeners)

```java
@Slf4j
@Profile("!real")
@Component
class ApplicationReadyEventListener implements ApplicationListener<ApplicationReadyEvent> {
  @Override
  public void onApplicationEvent(ApplicationReadyEvent event) {
    Hooks.onOperatorDebug();
    Hooks.onNextError((throwable, o) -> {
      log.error("TARGET OBJECT : {}", o, throwable);

      return o;
    });
  }
}
```

#### Context

Using Flux/Mono to handle stream operations, `ThreadLocal` based-data is no longer supported in non-blocking world.

> The execution can also easily and often jump from one thread to another.

Reactor now supports `Context` that is chain-scoped, is an good alternative of thread-based one.

```java
/* Basic get/set */
@Test
public void simpleTest() {
  Mono.just("[Hello]")
    .flatMap(o -> {
      Mono<Context> context = Mono.subscriberContext();   // getContext
      Mono<String> map = context.map(ctx -> o
                                     + ctx.get("key")
                                     + ctx.getOrDefault("no-key", ", reactor1."));

      return map;
    })
    .subscriberContext(context -> context.put("key", " World1")) // setContext, closest to 1st #get("key")
    .subscriberContext(context -> context.put("key", " world2")) // setContext
    .subscribe(o -> log.info("{}", o));
}
// consloe 결과
[Hello] World1, reactor1.
```

The fetched value with given key is coming on the closest put action. so key=World1 is provided in #get("key") operations.

But what happened when multiple #get is operated in the middle of chain?

```java
/* Basic propagation */
@Test
public void propagationTest() {
  Mono.just("[Hello]")
    .flatMap(o -> {
      Mono<Context> context = Mono.subscriberContext();   // getContext
      Mono<String> map = context.map(ctx -> o
                                     + ctx.get("key") // (1)
                                     + ctx.getOrDefault("no-key", ", reactor1."));

      return map;
    })
    .subscriberContext(context -> context.put("key", " World1")) // setContext, closest to 1st #get("key")
    .flatMap(o -> {
      Mono<Context> context = Mono.subscriberContext();   // getContext
      Mono<String> map = context.map(ctx -> o
                                     + ctx.get("key") // (2)
                                     + ctx.getOrDefault("no-key", ", reactor2"));

      return map;
    })
    .subscriberContext(context -> context.put("key", " world2")) // setContext, closest to 2nd #get("key")
    .subscribe(o -> log.info("{}", o));
}
// console 결과
[Hello] World1, reactor1. world2, reactor2
```

(1) is fetching value in #put("key", "World1") that is the closest in downstream-chain, (2) is infected of #put("key", "World2").

A Stream started with `Mono#subscriberContext` is reverse-direction referenced in chain:

```java
/* subscriberContext-chain */
@Test
public void startStreamTest() {
  Mono<String> r = Mono.subscriberContext() // stream of context
    .map(ctx -> ctx.put("key", "hello"))
    .flatMap(ctx -> Mono.just(ctx))
    .map(ctx -> ctx.getOrDefault("key", "no-hello")); // key already exists!
}
// console 결과
hello
```

Context is especially useful in carrying `requestID` in each hop of the thread in async-chain which is key role in servlet-based MDC utils. MDC keeps data in threadlocal but It is not working in multi-thread condition. Here is the brief sample:

**MDC practice**

```java
/* Alternative of ThreadLocal: Context */
@Test
public void solvedOfContext() throws InterruptedException {
  // given
  String requestId = UUID.randomUUID().toString();
  ThreadLocal<String> mdc = new ThreadLocal();
  mdc.set(requestId);

  log.info("[{}] start", mdc.get());

  // when
  Flux.range(0, 10)
    .flatMap(o -> Mono.subscriberContext() // getContext#1
             .map(ctx -> {
               log.info("[{}] {}", ctx.get("requestId"), o);

               return ctx;
             })
             .map(ctx -> o))
    .handle((o, handler) -> {	// getContext#2
      log.info("[{}] {}", handler.currentContext().get("requestId"), o);

      handler.next(o);
    })
    .subscriberContext(Context.of("requestId", mdc.get()))
    .subscribeOn(Schedulers.elastic())
    .subscribe();

  Thread.sleep(500);
}
```

#### Clean-Up

**doOnDiscard**

The elements what couldn't pass through next-chain after filter are catched in `doOnDiscard`.

```java
@Test
public void discardLocalMultipleFilters() {
  AtomicInteger numberCount = new AtomicInteger(0);
  AtomicInteger stringCount = new AtomicInteger(0);
  AtomicInteger objectCount = new AtomicInteger(0);

  StepVerifier.create(Flux.range(1, 12)
			.hide() //hide both avoid the fuseable AND tryOnNext usage
			.filter(i -> i % 2 == 0) // Flux<Integer>
			.map(String::valueOf)
			.filter(s -> s.length() < 2) // Flux<String>
			.doOnDiscard(Number.class, i -> numberCount.incrementAndGet())
			.doOnDiscard(String.class, i -> stringCount.incrementAndGet())
			.doOnDiscard(Object.class, i -> objectCount.incrementAndGet()))
    .expectNext("2", "4", "6", "8")
    .expectComplete()
    .verify();

  assertThat(numberCount).hasValue(6); // 1 3 5 7 9 11
  assertThat(stringCount).hasValue(2); // 10 12
  assertThat(objectCount).hasValue(8); // all
}
```