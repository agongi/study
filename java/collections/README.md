## Java Collections Framework

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - https://en.wikipedia.org/wiki/Java_collections_framework
 - http://www.gliderwiki.org/wiki/99
```

<img src="https://github.com/agongi/study/blob/master/java/collections/images/Screen%20Shot%202016-02-14%20at%2018.31.04.png" width="75%">

### Nutshell
#### Read-Only Collections
- Map<K, V> map = Collections.unmodifiableMap();
- List<E> list = Collections.unmodifiableList();
- Set<E> set = Collections.unmodifiableSet();

### [1. List](https://github.com/agongi/study/tree/master/java/collections/list/)
- duplicated : ok
- `ordering : ok`

  - Vector
  - ArrayList
  - LinkedList

### [2. Set](https://github.com/agongi/study/tree/master/java/collections/set/)
- duplicated : no
- `ordering : no`

  - HashSet
  - LinkedHashSet
  - TreeSet

### [3. Map](https://github.com/agongi/study/tree/master/java/collections/map/)
key, value structure

- duplicated (key) : no
- duplicated (value) : ok
- ordering : no

  - HashTable
  - HashMap
  - LinkedHashMap
  - TreeMap
  - SkipListMap
