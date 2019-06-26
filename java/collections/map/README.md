## Map

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.oracle.com/javase/tutorial/collections/implementations/map.html
- http://stackoverflow.com/questions/40471/differences-between-hashmap-and-hashtable
- http://javarevisited.blogspot.kr/2015/08/difference-between-HashMap-vs-TreeMap-vs-LinkedHashMap-Java.html
- http://javarevisited.blogspot.kr/2013/01/difference-between-identityhashmap-and-hashmap-java.html
- http://javarevisited.blogspot.kr/2016/08/how-to-iterate-through-ConcurrentHashMap-print-all-keys-values-java.html
```

### Map Implementations
#### HashMap
No order

```java
Map<String, Integer> map = new HashMap();

map.put("no", 1);
map.remove("no", 1);
```

#### LinkedHashMap
Insert-order or access-order

```java
Map<String, Integer> map = new LinkedHashMap();

map.put("no", 1);
map.remove("no", 1);
```

#### TreeMap
Natural-order

```java
SortedMap<String, Integer> sortedMap = new TreeMap<>();

sortedMap.put("1", 1);
sortedMap.put("2", 2);
sortedMap.put("3", 3);

sortedMap.firstKey();
sortedMap.lastKey();

sortedMap.headMap("2"); // from ........
sortedMap.tailMap("2"); // .......... to
```

```java
// NavigableMap extends SortedMap interface providing more methods
NavigableMap<String, Integer> navigableMap = new TreeMap<>();

navigableMap.put("1", 1);
navigableMap.put("2", 2);
navigableMap.put("3", 3);

navigableMap.ceilingEntry("2"); // greater or equals: 2
navigableMap.floorEntry("2");   // less or equals: 2

navigableMap.higherEntry("2");  // greater: 3
navigableMap.lowerEntry("2");   // less: 1
```
> Comparable is required to sort or specify Comparator

> General performance for insert/delete is slower than HashMap

#### EnumMap
Special purposed map to contain enums

- Natural-order

```java
public enum STATE {
    NEW, RUNNING, WAITING, FINISHED;
}

EnumMap<STATE, String> stateMap = new EnumMap<STATE, String>(STATE.class);

stateMap.put(STATE.RUNNING, "Program is running");
stateMap.put(STATE.WAITING, "Program is waiting");
```

#### IdentityHashMap
Use **==** operator to find entry instead of hashCode(), equals()

- Better performance than hash based
- Check reference equality than logical equality

#### WeakHashMap
This wraps keys as WeakReference

- keys are candidates of removal on next GC

### Concurrent packages
<img src="images/Screen%20Shot%202017-08-19%20at%2002.05.18.png" width="75%">

#### ConcurrentHashMap
- partial row-lock on write
  - better performance than ``Collections.synchronizedMap()``

```java
Integer value = map.get("no2");
if (value == null) {
    map.put("no2", 2);
}

// equals to
map.putIfAbsent("no2", 2);
```

```java
Integer value = map.get("1");
if (value == null) {
    value = 1;
}
// equals to (key, defaultValue)
map.getOrDefault("1", 1);
```

```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

map.put("1", 1);
map.put("2", 2);
map.put("3", 3);

map.compute("1", (k, v) -> v + 1);  // 2
map.computeIfPresent("1", (k, v) -> v + 3); // 4
map.computeIfAbsent("4", k -> Integer.valueOf(k));  // key=4, value=4
```

#### ConcurrentSkipListMap
Natural-order

- read no-lock / write lock

#### Collections.synchronizedMap(new HashMap())
- entire table-lock on read/write
  - low performance
