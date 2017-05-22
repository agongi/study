## Java Generics
Java fundamental of Generics.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.24
ㅁ Origin: Effective Java 2nd edition
ㅁ References:
 - https://en.wikipedia.org/wiki/Generics_in_Java
 - https://docs.oracle.com/javase/tutorial/java/generics/index.html
 - http://ohgyun.com/51
```

### Motivation
- `java.lang.ClassCastException` check in compile-time
- Elimination of casts

### Generic Types
#### Raw Types
```java
/**
 * Generic class or interface without any type arguments
 * @param <T> the type of the value being boxed
 */
public class Box<T> {
    // T stands for "Type"
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```

> A type variable T can be any **non-primitive** type

#### Naming Conventions
- E - Element (used extensively by the Java Collections Framework)
- K - Key
- V - Value
- N - Number
- T - Type
- S,U,V etc. - 2nd, 3rd, 4th types

### Generic Methods
This is similar to declaring a generic type, but the type parameter's **scope is limited to the method** where it is declared

```java
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}

public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey()   { return key; }
    public V getValue() { return value; }
}
```

> Generic methods need to addressed **before method return type**

### Bounded Type Parameters
It can restrict the types to only accept instances of Number or its subclasses. The syntax `extends` is used to make sense to mean either "extends" (as in classes) or "implements" (as in interfaces)

```java
public class Box<T> {

    private T t;          

    public void set(T t) {
        this.t = t;
    }

    public T get() {
        return t;
    }

    public <U extends Number> void inspect(U u){
        System.out.println("T: " + t.getClass().getName());
        System.out.println("U: " + u.getClass().getName());
    }

    public static void main(String[] args) {
        Box<Integer> integerBox = new Box<Integer>();
        integerBox.set(new Integer(10));
        integerBox.inspect("some text");  // error: this is still String!
    }
}
```

#### Multiple Bounds
```java
Class A { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }

<T extends A & B & C>  // ok, A is class
<T extends B & A & C>  // fail, B is interface
```

> If one of the bounds is a class, it must be specified first

#### Type Inference
```java
Map<String, List<String>> myMap = new HashMap<String, List<String>>();
// type inferred
Map<String, List<String>> myMap = new HashMap<>();
```

### Type wildcards
The question mark (?), called the wildcard, represents an unknown type

#### Unbounded Wildcards
```java
List<?> list
```

- read values as `Object`
- write only `null` values is allowed

#### Upper Bounded Wildcards
```java
List<? extends Custom>
```

- ? is subtypes of Custom
  - read values type of `Custom` or casting to subtype with `@SuppressWarnings("uncheked")`
  - write type of value that inherits or implements `Custom`

#### Lower Bounded Wildcards
```java
List<? super Custom>
```

- ? is supertypes of Custom

### Type Erasure

### Restrictions on Generics
#### Cannot Instantiate Generic Types with Primitive Types
```java
Pair<int, char> p = new Pair<>(8, 'a');  // compile-time error
```

#### Cannot Create Instances of Type Parameters
```java
public static <E> void append(List<E> list) {
    E elem = new E();  // compile-time error
    list.add(elem);
}
```

As a workaround, you can create an object of a type parameter through reflection:
```java
public static <E> void append(List<E> list, Class<E> cls) throws Exception {
    E elem = cls.newInstance();   // OK
    list.add(elem);
}
```

### Cannot Declare Static Fields Whose Types are Type Parameters
```java
public class MobileDevice<T> {
    private static T os;

    // ...
}

MobileDevice<Smartphone> phone = new MobileDevice<>();
MobileDevice<Pager> pager = new MobileDevice<>();
MobileDevice<TabletPC> pc = new MobileDevice<>();
```
static field represents class-level variables shared by all objects of the class. It can't have different types at the same time

### Cannot Use Casts or instanceof with Parameterized Types
The Java compiler erases all type parameters in generic code in compile-time, you cannot verify which parameterized type for a generic type is being used at runtime:

```java
public static <E> void rtti(List<E> list) {
    if (list instanceof ArrayList<Integer>) {  // compile-time error
        // ...
    }
}
```

### Cannot Create Arrays of Parameterized Types
```java
List<Integer>[] arrayOfLists = new List<Integer>[2];  // compile-time error
```
