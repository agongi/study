## Set

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.08.28
ㅁ References:
- https://docs.oracle.com/javase/tutorial/collections/implementations/set.html
- http://tutorials.jenkov.com/java-collections/set.html
- http://javarevisited.blogspot.kr/2012/11/difference-between-treeset-hashset-vs-linkedhashset-java.html
- http://javarevisited.blogspot.kr/2014/03/how-to-use-enumset-in-java-with-example.html
```

<img src="https://github.com/agongi/study/blob/master/java/collections/set/images/Screen%20Shot%202016-02-15%20at%2001.51.47.png" width="75%">

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
thread-safe

non-sorted

#### ConcurrentSkipListSet
thread-safe

sorted
