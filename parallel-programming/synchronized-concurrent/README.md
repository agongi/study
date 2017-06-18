## Synchronized vs Concurrent Collections
Java conceptual comparison between Synchronized and Concurrent Collections function.

>###### @Synchronized annotation locks Collection(table) widely. ConcurrentHashMap or CopyOnWriteArrayList only locks segment(column) lock.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.06.02
ㅁ References:
 - http://javarevisited.blogspot.kr/2016/05/what-is-difference-between-synchronized.html
```

#### Synchronized

 - thread-safe
 - collection-wide lock


#### Concurrent Collections

 - thread-safe
 - segment lock

<img src="https://github.com/agongi/study/blob/master/parallel-programming/synchronized-concurrent/images/Internal%20implementation%20of%20ConcurrentHashMap%20in%20Java.png" width="75%">
