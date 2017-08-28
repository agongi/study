## Set

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.08.28
ㅁ References:
- https://docs.oracle.com/javase/tutorial/collections/implementations/set.html
- http://tutorials.jenkov.com/java-collections/set.html
- http://javarevisited.blogspot.kr/2012/11/difference-between-treeset-hashset-vs-linkedhashset-java.html
- http://javarevisited.blogspot.kr/2014/03/how-to-use-enumset-in-java-with-example.html
- https://stackoverflow.com/questions/6720396/different-types-of-thread-safe-sets-in-java
```

<img src="images/Screen%20Shot%202017-08-28%20at%2022.03.11.jpg" width="75%">

### Set Implementations
#### HashSet
No order

```java
Set set = new HashSet();

set.add("name");
set.remove("name");
```

#### LinkedHashSet
Insertion-order

```java
Set set = new LinkedHashSet();

set.add("name");
set.remove("name");
```

#### TreeSet
Natural-order

```java
SortedSet set = new TreeSet();

set.first();
set.last();

set.tailSet("from");  // from ........
set.headSet("to");    // .......... to
```

```java
// NavigableSet extends SortedSet interface providing more methods
NavigableSet<Integer> set = new TreeSet();
set.add(1);
set.add(2);
set.add(3);

set.ceiling(2); // greater or equals: 2
set.floor(2);   // less or equals: 2

set.higher(2);  // greater: 3
set.lower(2);   // less: 1
```

> Comparable is required to sort or specify Comparator

> General performance for insert/delete is slower than HashSet

#### EnumSet
EnumSet is a Set contains enum instance of a specific enum type, in a more efficient way than others (like HashSet, TreeSet, etc.)

```java
EnumSet<Size> largeSize = EnumSet.of(Size.XXXL, Size.XXL, Size.XL, Size.L);
for(Size size: largeSize) {
    log.debug("size: {}", size);
}
```

### Concurrent packages
#### CopyOnWriteArraySet
변경이 있으면 전체 Set 을 복사해서 새로 생성 (CopyOnWrite)

Iterator 는 생성 당시 snapshot 을 가지고 동작하고, 도중에 다른 쓰레드에 의해 변경되어도 origin 은 유지된다 (변경된 값은 새로운 Copy 에 적용되어 있지 - CopyOnWrite)

단점은 변경될때 마다 전체 Set 을 복사한다는 점. 사이즈의 크면 복사 비용이 크다는 점

CopyOnWriteArraySet is best suited as read-only collection whose size is small enough to copy if some mutative operation happens

> unmodifiableSet is never changed

#### ConcurrentSkipListSet
read no-lock
write lock
sorted

#### Collections.synchronizedSet
read lock
write lock
