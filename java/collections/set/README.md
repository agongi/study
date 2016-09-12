## Set
Java conceptual comparison among HashSet, LinkedHashSet and TreeSet collections.

>###### TBD

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - http://www.programcreek.com/2013/03/hashset-vs-treeset-vs-linkedhashset/
 - http://javarevisited.blogspot.kr/2012/11/difference-between-treeset-hashset-vs-linkedhashset-java.html
```
<img src="https://github.com/agongi/study/blob/master/java/collections/set/images/Screen%20Shot%202016-02-15%20at%2001.51.47.png" width="75%">

#### 1. HashSet
HashSet is Implemented using a HashMap.

 - Non-synchronized
 - Fast
 - Allows one null
 - `No order`

> If you want to make a HashSet thread-safe, but there is no ConcurrentHashSet in current JDK. you can derive it from ConcurrentHashMap as following: <br>
`Collections.newSetFromMap(new ConcurrentHashMap<Object,Boolean>())`

#### 2. LinkedHashSet
LinkedHashSet is Implemented using a LinkedHashMap.

 - Non-synchronized
 - Fast
 - Allows one null
 - `Insertion-order`

> LinkedHashSet is a `subclass of HashSet`. That means it inherits the features of HashSet. In addition, the linked list preserves the `insertion-order`.

#### 3. TreeSet
TreeSet is Implemented using a TreeMap.

 - Non-synchronized
 - `Slower than HashSet or LinkedHashSet`
 - Doesn't allows null
 - `Natural-order (needs to implement Comparable interface)`

> key comparison is required in tree features for natural-order.
