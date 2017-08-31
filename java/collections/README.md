## Java Collections

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - https://en.wikipedia.org/wiki/Java_collections_framework
 - http://tutorials.jenkov.com/java-collections/index.html
```

<img src="https://github.com/agongi/study/blob/master/java/collections/images/Screen%20Shot%202016-02-14%20at%2018.31.04.png" width="75%">

### In a Nutshell
#### Read-Only Collections
- Map<K, V> map = Collections.unmodifiableMap();
- List<E> list = Collections.unmodifiableList();
- Set<E> set = Collections.unmodifiableSet();

#### Concurrency
- Set<E> set = Collections.synchronizedSet(new HashSet());
- Set<E> set = new ConcurrentHashMap<>().keySet();
- Map<K,V> map = Collections.synchronizedMap(new HashMap());

### [List](https://github.com/agongi/study/tree/master/java/collections/list/)
Duplicated element is allowed

- ArrayList
- LinkedList
- CopyOnWriteArrayList

### [Set](https://github.com/agongi/study/tree/master/java/collections/set/)
Duplicated is not allowed

- HashSet
- LinkedHashSet
- TreeSet
  - SortedSet
  - NavigableSet
- EnumSet
- CopyOnWriteArraySet
- ConcurrentSkipListSet (TreeSet over concurrency)

### [Map](https://github.com/agongi/study/tree/master/java/collections/map/)
key - value pair structure

- HashMap
- LinkedHashMap
- TreeMap
  - SortedMap
  - NavigableMap
- EnumMap
- ConcurrentHashMap
- ConcurrentSkipListMap (TreeMap over concurrency)
- IdentityHashMap
- WeakHashMap
- ~~ConcurrentNavigableMap~~

> Do not use Vector or HashTable those are introduced in early JDK and internally synchronized instead of concurrent package
