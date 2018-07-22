## Java Generics

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.oracle.com/javase/tutorial/java/generics/index.html
- https://stackoverflow.com/questions/745756/java-generics-wildcarding-with-multiple-classes
- https://stackoverflow.com/questions/5207115/java-generics-t-vs-object
- https://stackoverflow.com/questions/18176594/when-to-use-generic-methods-and-when-to-use-wild-card
- https://stackoverflow.com/questions/24391123/object-vs-classt-vs-class-in-java
- http://ohgyun.com/51
```

### Motivation
- Prevent `java.lang.ClassCastException` in runtime
- No more casting

<img src="images/Screen%20Shot%202017-09-09%20at%2014.55.03.gif" width="75%">

### Type Parameters
```java
public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```

> Naming Conventions

- E - Element (used extensively by the Java Collections Framework)
- K - Key
- V - Value
- N - Number
- T - Type
- S, U, V etc., - 2nd, 3rd, 4th types

#### Upper Bounded Type Parameters
It can restrict the types to only accept instances of Number or its subclasses

```java
public class Box<T> {
    public <E extends Number> void inspect(E e){
        // ...
    }

    public static void main(String[] args) {
        Box<Integer> integerBox = new Box<Integer>();        
        integerBox.inspect("some text");  // compile-error
    }
}
```

#### Lower Bounded Type Parameters
It doesn't supported

#### Multiple Bounds
If one of the bounds is a class, it must be specified first

```java
Class A     { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }

<T extends A & B & C>  // ok, A is class
<T extends B & A & C>  // fail, B is interface
```

### Wildcards
```java
public class BoxUtils {
  public Object get(List<?> list) {
    return list.get(0);
  }
}
```

- read: `Object`
- write: Not allowed except `null`

#### Upper Bounded Wildcards
```java
List<? extends Custom>
```

- read: `Custom` or casting with `@SuppressWarnings("uncheked")`
- write: inherits or implements `Custom`

#### Lower Bounded Wildcards
```java
List<? super Custom>
```

#### Multiple Bounds
It doesn't supported

### Differences
#### [Type Parameters vs Wildcard](https://stackoverflow.com/questions/18176594/when-to-use-generic-methods-and-when-to-use-wild-card)
```java
// ensure dest, src represent exactly the same type
public static <T extends Number> void copy(List<T> dest, List<T> src)

// can't be ensured
public static void copy(List<? extends Number> dest, List<? extends Number> src)
```

#### \<?> vs \<T>
- T - reusable
- ? - once

### Restrictions on Generics
#### Cannot Create Instances of Type Parameters
```java
public static <E> void append(List<E> list) {
    E elem = new E();  // compile-time error
    list.add(elem);
}
```

As a workaround, you can create an object of a type parameter through reflection :
```java
public static <E> void append(List<E> list, Class<E> cls) throws Exception {
    E elem = cls.newInstance();   // OK
    list.add(elem);
}
```

#### Cannot Declare Static Fields of Type Parameters
```java
public class MobileDevice<T> {
    private static T os;

    // ...
}
```
static field represents class-level variables shared by all objects of the class. It can't have different types at the same time

#### Cannot Use Casts or instanceof with Parameterized Types
The Java compiler erases all type parameters in generic code in compile-time, you cannot verify which parameterized type for a generic type is being used at runtime:

```java
public static <E> void rtti(List<E> list) {
    if (list instanceof ArrayList<Integer>) {  // compile-time error
        // ...
    }
}
```

#### Cannot Create Arrays of Parameterized Types
```java
E[] arrayOfLists = new E[];  // compile-time error
```
