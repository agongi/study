## List
Java conceptual comparison among Vector, ArrayList and LinkedList collections.

>###### TBD

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - http://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist
 - http://www.javatpoint.com/difference-between-arraylist-and-linkedlist
 - http://javarevisited.blogspot.kr/2012/02/difference-between-linkedlist-vs.html
```

Elements have a `specific order`, and `duplicate elements are allowed`. Elements can be placed in a specific position. As such, elements can be found by `index`.

#### ~~1. Vector (Deprecated)~~
Thread-safe
Slow

> The only reason to use Vector is when a legacy API (from ca. 1996) requires it.

#### 2. ArrayList
Non thread-safe

- Pros :<br>
 search

- Cons :<br>
 add/remove

> useful when it is used get() frequently condition.

> `ConcurrentLinkedQueue` helps you when there is needs of thread-safe.

#### 3. LinkedList
Non thread-safe

- Pros :<br>
 add/remove

- Cons :<br>
 search

> useful when it is used add()/remove() frequently condition.
