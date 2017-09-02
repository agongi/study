## List

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - http://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist
 - http://www.javatpoint.com/difference-between-arraylist-and-linkedlist
 - http://javarevisited.blogspot.kr/2012/02/difference-between-linkedlist-vs.html
```

### List Implementations
#### ArrayList
Good for **random access** based on index
  - get

Bad for re-indexing condition
  - add/remove

#### LinkedList
Good for sequential access or insert/delete
  - add/remove/iterator

Bad random access
  - get

### Concurrent packages
#### CopyOnWriteArrayList
Copy entire List on write
  - Iteration can keep origin snapshot while other thread changes the value of it

Good for read-only collection whose size is small enough to copy rarely if change happens
