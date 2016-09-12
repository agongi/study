## Iterator vs foreach vs for
Java conceptual comparison between Iterator(), foreach() and for() function.

>###### Traditional for is the best performance. Iterator() supports to remove items inside of iteration without ConcurrentModificationException but foreach doesn't allow you to do it.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.15
ㅁ References:
 - http://stackoverflow.com/questions/2113216/which-is-more-efficient-a-for-each-loop-or-an-iterator
```

**Iterator**
performance is the same with foreach.

but It supports this following:

```java
Set<Object> set = new HashSet<Object>();
// add some items to the set

Iterator<Object> setIterator = set.iterator();
while(setIterator.hasNext()){
     Object o = setIterator.next();
     if(o meets some condition){
          setIterator.remove();
     }
}
```

**foreach**
performance is equal to iterator.

but It doesn't support this following:

```java
Set<Object> set = new HashSet<Object>();
// add some items to the set

for(Object o : set){
     if(o meets some condition){
          set.remove(o);  // ConcurrentModificationException
     }
}
```

**for**
pros
 : performance

cons
 : a bit complicated to use compared to iterator and foreach.
 : calculates length of array gets performance down.
  > for(int i=0; i<array.length(); i++) {...}

  > int length = array.length(); for(int i=0; i<length; i++) {...}
