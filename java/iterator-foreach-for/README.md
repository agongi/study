## Iterator vs foreach vs for

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.15
ㅁ References:
 - http://stackoverflow.com/questions/2113216/which-is-more-efficient-a-for-each-loop-or-an-iterator
 - https://stackoverflow.com/questions/3329842/how-to-get-the-current-loop-index-when-using-iterator
```

### Iterator
CME(ConcurrentModificationException) safe

```java
Set<Object> set = new HashSet<>();

// modify while iteration
Iterator<Object> setIterator = set.iterator();
while(setIterator.hasNext()) {
    Object o = setIterator.next();

    if(true) {  // possible
        setIterator.remove();
    }
}
```

```java
List<String> list = Arrays.asList("zero", "one", "two", "three");

for (ListIterator<String> it = list.listIterator(); it.hasNext(); ) {
    String element = it.next();

    if (it.hasNext()) {
      System.out.println("next: " + it.next());
      System.out.println("nextIndex: " + it.nextIndex());  
    }

    if (it.hasPrevious()) {
      System.out.println("previous: " + it.previous());
      System.out.println("previousIndex: " + it.previousIndex());  
    }
}
```

### foreach
CME not safe

```java
Set<Object> set = new HashSet<>();

// modify while iteration
for(Object obj : set) {

    if (true) { // impossible - CME
        set.remove(o);  
    }
}
```

### for
Best performance
