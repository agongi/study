## Java Generics

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.24
ㅁ Origin: Effective Java 2nd edition
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

### Generic Types
#### Type Parameters
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

Type variables can only be **non-primitive** type

#### Bounded Type Parameters
It can restrict the types to only accept instances of Number or its subclasses. The syntax `extends` is used to make sense to mean either "extends" (as in classes) or "implements" (as in interfaces)

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

> Multiple Bounds

If one of the bounds is a class, it must be specified first
```java
Class A { /* ... */ }
interface B { /* ... */ }
interface C { /* ... */ }

<T extends A & B & C>  // ok, A is class
<T extends B & A & C>  // fail, B is interface
```

### Generics, Inheritance, and Subtypes
<img src="images/Screen%20Shot%202017-09-09%20at%2014.55.03.gif" width="75%">

### Wildcards
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

#### [Object vs Class<?>](https://stackoverflow.com/questions/24391123/object-vs-classt-vs-class-in-java)
```java
private class SimpleClass {
    private Integer keyboard;
    private String mouse;
}


@Test
public void test2() {
    SimpleClass simpleClass = new SimpleClass();
    // param Object
    doWithObject(simpleClass);

    // param Class<?>
    doWithClass(SimpleClass.class);
    doWithClass(simpleClass.getClass());

}

private void doWithObject(Object obj) {
    System.out.println(obj.getClass().getSimpleName());
}

private void doWithClass(Class<?> clz) {
    System.out.println(clz.getSimpleName());
}
```

#### \<?> vs \<T>
Use ? if it is used once, but T if this is reusable

### Generic Methods
```java
public class CompareUtils {
    public static <K, V> boolean comparePair(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}
```

### Type Inference
#### Instantiation of Generic Classes
```java
Map<String, List<String>> myMap = new HashMap<String, List<String>>();
// type inferred
Map<String, List<String>> myMap = new HashMap<>();
```

#### Target Types
emptyList returns a value of type List<T>, the compiler infers that the type argument <T> must be the value String

```java
List<String> list = Collections.<String>emptyList();
// target type inference
List<String> list = Collections.emptyList();
```

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

As a workaround, you can create an object of a type parameter through reflection :
```java
public static <E> void append(List<E> list, Class<E> cls) throws Exception {
    E elem = cls.newInstance();   // OK
    list.add(elem);
}
```

#### Cannot Declare Static Fields Whose Types are Type Parameters
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
List<Integer>[] arrayOfLists = new List<Integer>[2];  // compile-time error
```
