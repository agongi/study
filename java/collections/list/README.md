## List

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.oracle.com/javase/tutorial/collections/implementations/list.html
 - http://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist
 - http://www.javatpoint.com/difference-between-arraylist-and-linkedlist
 - http://javarevisited.blogspot.kr/2012/02/difference-between-linkedlist-vs.html
```

### ArrayList

Commonly used random-access link.

- pros: **random access** based on index
    - get
  - cons: re-indexing condition

      - add/remove

### LinkedList

LinkedList is used to search from start to end.

- pros: **sequential access** or insert/delete
    - add/remove/iterator
  - cons: random access

      - get

#### CursorableLinkedList

subset of linkedList, but capable in iteration at the passed fromIndex

### Concurrent packages
#### CopyOnWriteArrayList

Copy entire List on write
  - Iteration can keep origin snapshot while other thread changes the value of it

Good for read-only collection whose size is small enough to copy rarely if change happens
