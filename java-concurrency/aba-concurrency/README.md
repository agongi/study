## ABA Concurrency

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.baeldung.com/cs/aba-concurrency
```

ABA 는 동시성 상황에서

- A reads value=1
  - 작업수행
- B writes value=2
- B writes value=1 (rollback)
- A writes value
  - compare (1 ==1) 로 변경확인 -> value 가 최초와 동일하므로, write 수행

```
Thread-A
	|	
value
	|
Thread-B
```

일반적인 상황에서는 문제가 없지만 (value is equals), integrity 관점에서 봤을때는 변경이 있지만 없다고 판단되므로 문제가 발생 할 수 있다.

해결하기 위해선 version 관리가 필요하다.

> 즉 value 변경에 대한 history (==version) 을 관리

## [AtomicStampedReference](https://www.baeldung.com/java-atomicstampedreference)

```java
public static void main(String[] args) {
  // declares
  AtomicStampedReference<Integer> amount = new AtomicStampedReference<>(10, 0);
	// get value & stamp
  Integer balance = amount.getReference();
  int version = amount.getStamp();

  amount.compareAndSet(balance, balance + 100, version, ++version);
}
```

AtomicStampedReference#compareAndSet 를 보면 

- [0]: compare 할 value, 현재 공유자원의 value 가 입력값과 동일해야한다.
- [1]: update 할 value
- [2]: compare 할 stamp, 현재 공유자원의 stamp 가 입력값과 동일해야한다.
- [3]: update 할 stamp

Stamp 를 통해 변경이력도 관리되므로, ABA-Problem 처럼 value#equals 만으로 해결할 수 없는 문제의 회피가 가능하다.

> 낙관적락 (optimistic-lock) 의 방식과 동일

## [AtomicMarkableReference](https://www.baeldung.com/java-atomicmarkablereference)

AtomicStampedReference 와 동일하지만, stamp 에 int 가 아닌 boolean 이 들어간다는게 차이점이다.

일회성으로 dirty-check 를 하는 경우에는 유용하다.



