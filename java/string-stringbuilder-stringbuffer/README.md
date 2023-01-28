## String vs StringBuilder vs StringBuffer
Java conceptual comparison among String, StringBuilder and StringBuffer keyword.

>###### String is immutable and fast, It is good choice to use `String` when modifier operation is not needed.

```
ㅁ Author: suktae.choi
- http://javahungry.blogspot.com/2013/06/difference-between-string-stringbuilder.html
- http://stackoverflow.com/questions/2971315/string-stringbuffer-and-stringbuilder
```

#### 1. String
 - Another object gets created when value is changed.
 - Stored in `Permanent Generation`.

> If your string is not going to change use a String class because a String object is immutable.

#### 2. StringBuilder
 - Origin object is changed when value is changed.
 - Stored in `Heap`.

> If your string can change (example: lots of logic and operations in the construction of the string) and will only be accessed from a single thread, using a StringBuilder is good enough

#### 3. StringBuffer
 - Origin object is changed when value is changed.
 - Stored in `Heap`.

> If your string can change, and will be accessed from multiple threads, use a StringBuffer because StringBuffer is synchronous so you have thread-safety.

![alt-string-stringbuilder-stringbuffer](https://github.com/agongi/study/blob/master/java/string-stringbuilder-stringbuffer/images/Screen%20Shot%202016-02-14%20at%2015.50.02.png)
