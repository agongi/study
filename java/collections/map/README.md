## Map
Java conceptual comparison among HashMap, HashTable, LinkedHashMap and TreeMap collections.

>###### Map series saves {key, value} pair.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - http://stackoverflow.com/questions/40471/differences-between-hashmap-and-hashtable
 - http://www.javatpoint.com/difference-between-hashmap-and-hashtable
 - http://www.programcreek.com/2013/03/hashmap-vs-treemap-vs-hashtable-vs-linkedhashmap/
 - http://javarevisited.blogspot.kr/2015/08/difference-between-HashMap-vs-TreeMap-vs-LinkedHashMap-Java.html
```

<img src="https://github.com/agongi/study/blob/master/java/collections/map/images/Screen%20Shot%202016-02-14%20at%2022.41.14.png" width="75%">

#### ~~1. HashTable (Deprecated)~~
 - Synchronized
 - Slow
 - Doesn't allow any null key or value

> The only reason to use HashTable is when a legacy API (from ca. 1996) requires it.

#### 2. HashMap
The HashMap class is roughly equivalent to Hashtable, except that it is `non synchronized` and `permits nulls`.

 - Non-synchronized
 - Fast
 - Allows one null key and multiple null values
 - `No order`

> If you want to make a HashMap thread-safe, use [Collections.synchronizedMap() or `ConcurrentHashMap.](http://www.pixelstech.net/article/1394026282-ConcurrentHashMap-vs-Collections-synchronizedMap)

> HashMap is better choice in modern-java program, It is better to implement synchronize function externally on demand.

#### 3. LinkedHashMap
 - Non-synchronized
 - Fast
 - Allows one null key and multiple null values
 - `Insertion-order`

> LinkedHashMap is a `subclass of HashMap`. That means it inherits the features of HashMap. In addition, the linked list preserves the `insertion-order`.

#### 4. TreeMap
 - Non-synchronized
 - `Slower than HashMap or LinkedHashMap`
 - Allows only multiple null values not in key
 - `Natural-order (needs to implement Comparable interface)`

> Key comparison is required in tree features for natural-order.
