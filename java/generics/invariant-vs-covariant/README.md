## Invariant vs Covariant

```
@author: suktae.choi
- https://jackjeong.tistory.com/54?category=802500
```

### Covariant

Array 는 covariant (공변) 이다. 즉

```java
public class Animal {...}
public class Dog extends Animal {...}
```

의 상속관계일때, `Dog[] 은 Animal[] 의 subtype` 이 성립한다.

또한 타입이 runtime 에 남아있어서, 잘못된 타입의 CUD 발생시 ArrayStoreException 이 발생한다.

```java
Object strings[] = new String[3];
strings[0] = new Integer(0);	// ArrayStoreException
```

### Invariant

Collection 은 invariant 하다. 즉 동일한 상속관계일때

`List<Dog> 는 List<Animal>` 의 subtype 이 아니다.

또한 타입이 runtime 때 삭제되어 (type erase 를 통한 raw type 으로 남음. 호환성위함) runtime 에는 exception 미발생한다. 

> 대신 compile-time 때 error 로 잡아야함

