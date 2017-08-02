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

#### Sorted Collections
- SortedSet set = new TreeSet();
- NavigableSet set = new TreeSet();
- SortedMap<K, V> map = new TreeMap<>();
- NavigableMap<K, V> map = new TreeMap<>();

### [1. List](https://github.com/agongi/study/tree/master/java/collections/list/)
Duplicated element is allowed

- ArrayList
- LinkedList

### [2. Set](https://github.com/agongi/study/tree/master/java/collections/set/)
Duplicated is not allowed

- HashSet
- LinkedHashSet
- TreeSet
- ConcurrentSkipListSet

### [3. Map](https://github.com/agongi/study/tree/master/java/collections/map/)
key - value pair structure

- HashMap
- LinkedHashMap
- TreeMap
- ConcurrentSkipListMap
- ConcurrentNavigableMap

> Do not use Vector or HashTable those are introduced in early JDK and internally synchronized instead of concurrent package.
