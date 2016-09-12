## Java Generics
Java fundamental of Generics.

> They allow a type or method to operate on objects of `various types while providing compile-time type safety`. This feature specifies the type of objects stored in a Java Collection.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.24
ㅁ Origin: Effective Java 2nd edition
ㅁ References:
 - https://en.wikipedia.org/wiki/Generics_in_Java
 - https://docs.oracle.com/javase/tutorial/java/generics/index.html
```

### 1. Why to use Generics?
 - Strong type casting check in compile-time
 - Elimination of casting when get field from

### Type Parameter Naming Conventions
 - E - Element (used extensively by the Java Collections Framework)
 - K - Key
 - N - Number
 - T - Type
 - V - Value
 - S,U,V etc. - 2nd, 3rd, 4th types

#### HashSet<object> vs HashSet<?>
```java
public abstract class Child<M extends ParentType> extends Parent<M> implements InitializingBean { 

    protected Child(Class<M> clz) {         
        super(clz);         
    }  

    @Override 
    public void afterPropertiesSet() throws Exception {  }
}
```

> \[M extends ParentType\] literally means that M is retained by implements or extends ParentType.
