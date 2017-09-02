## Map

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
- http://stackoverflow.com/questions/40471/differences-between-hashmap-and-hashtable
- http://javarevisited.blogspot.kr/2015/08/difference-between-HashMap-vs-TreeMap-vs-LinkedHashMap-Java.html
- http://javarevisited.blogspot.kr/2013/01/difference-between-identityhashmap-and-hashmap-java.html
- http://javarevisited.blogspot.kr/2016/08/how-to-iterate-through-ConcurrentHashMap-print-all-keys-values-java.html
```

### Map Implementations
#### HashMap
The HashMap class is roughly equivalent to Hashtable, except that it is `non synchronized` and `permits nulls`.

 - Non-synchronized
 - Fast
 - Allows one null key and multiple null values
 - `No order`

#### LinkedHashMap
 - Non-synchronized
 - Fast
 - Allows one null key and multiple null values
 - `Insertion-order`

> LinkedHashMap is a `subclass of HashMap`. That means it inherits the features of HashMap. In addition, the linked list preserves the `insertion-order`.

#### TreeMap
 - Non-synchronized
 - `Slower than HashMap or LinkedHashMap`
 - Allows only multiple null values not in key
 - `Natural-order (needs to implement Comparable interface)`

> Key comparison is required in tree features for natural-order.

#### IdentityHashMap
: use == to compare instead hashCode(), equals()
: better speed due to == is simpler than those two
: used to check reference equality not logical equality

#### WeakHashMap
: WeakHashMap wraps keys as WeakReference (== keys are candidates of removed on next GC)

### Concurrent packages
<img src="images/Screen%20Shot%202017-08-19%20at%2002.05.18.png" width="75%">

#### ConcurrentHashMap
: partial-lock, half-concurrency

#### ConcurrentSkipListMap
: concurrent SortedMap

#### Collections.synchronizedMap(new HashMap())
: simple wrap of origin set with wrap synchronized-block in all methods
: read lock / write lock (entire table-lock, not row-lock)
