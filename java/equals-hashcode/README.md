## equals() and/or hashcode()
Java conceptual comparison between equals() and hashCode() method.

>###### Objects which are `.equals() == true` MUST have the same `.hashCode() == true`.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.25
ㅁ Origin: Effective Java 2nd
ㅁ References:
 - http://stackoverflow.com/questions/17027777/relationship-between-hashcode-and-equals-method-in-java
```

**Principle**

If you have two objects which are `.equals() == true` but `.hashCode() == false`, that is wrong!

**When to @Override equals() and/or hashCode() method?**

@Override object.equals() method when you need to `check equality of custom object`.

```java
equality comparison code example
```

@Override object.hashCode() method when you need to `use custom object as a key in HashMap`.<br>
HashMap saves and/or reference value based on hashCode of key.

```java
hashCode comparison code example
```
