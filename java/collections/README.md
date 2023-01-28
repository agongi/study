## Java Collections

```
ㅁ Author: suktae.choi
- https://en.wikipedia.org/wiki/Java_collections_framework
- http://tutorials.jenkov.com/java-collections/index.html
```

<img src="images/Screen%20Shot%202016-02-14%20at%2018.31.04.png" width="100%">

### In a Nutshell

#### Read-Only Collections

**java.util.Collections**

- List<E> list = Collections.unmodifiableList(new ArrayList());
- Set<E> set = Collections.unmodifiableSet(new HashSet());
- Map<K, V> map = Collections.unmodifiableMap(new HashMap());

**com.google.common.collect**

- ImmutableList#of
- ImmutableSet#of
- ImmutableMap#of

#### Concurrency
- List<E> list = Collections.synchronizedList(new ArrayList());
- Set<E> set = Collections.synchronizedSet(new HashSet());
  - Set<E> set = new ConcurrentHashMap<>().keySet();
- Map<K,V> map = Collections.synchronizedMap(new HashMap());
  - SortedMap<K, V> m = Collections.synchronizedSortedMap(new TreeMap());

### [List](list)
Duplicated element is allowed

- ArrayList
- LinkedList
  - CursorableLinkedList
- CopyOnWriteArrayList

### [Set](set)
Duplicated is not allowed

- HashSet
- LinkedHashSet
- TreeSet
  - SortedSet
  - NavigableSet
- EnumSet
- CopyOnWriteArraySet
- ConcurrentSkipListSet
  - SortedSet
  - NavigableSet

### [Map](map)
key - value pair structure

- HashMap
- LinkedHashMap
- TreeMap
  - SortedMap
  - NavigableMap
- EnumMap
- IdentityHashMap
- WeakHashMap
- ConcurrentHashMap
- ConcurrentSkipListMap
  - SortedMap
  - NavigableMap
  - ConcurrentNavigableMap

> Do not use Vector or HashTable those are introduced in early JDK and internally synchronized instead of concurrent package

### [Queue](queue)
FIFO, FILO structure

- ArrayBlockingQueue
- LinkedBlockingQueue
  - LinkedBlockingDeque
- ConcurrentLinkedQueue
- DelayQueue
- LinkedTransferQueue
- SynchronousQueue
- PriorityQueue
  - PriorityBlockingQueue

### [Heap](heap)

