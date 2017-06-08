## Nested Class

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ Origin: https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html
ㅁ References:
 - http://javarevisited.blogspot.kr/2012/12/inner-class-and-nested-static-class-in-java-difference.html
 - http://stackoverflow.com/questions/70324/java-inner-class-and-static-nested-class
 - http://stackoverflow.com/questions/1353309/java-static-vs-non-static-inner-class
 - http://docs.oracle.com/javase/tutorial/java/javaOO/nested.html
 - http://dicky-programmingjoy.blogspot.kr/2007/04/why-use-private-static-method.html
 - https://stackoverflow.com/questions/1953530/why-does-java-prohibit-static-fields-in-inner-classes
```

<img src="https://github.com/agongi/study/blob/master/java/nested-class/images/Screen%20Shot%202017-06-09%20at%2001.22.27.png">

### Purpose
- The class that is only used in one place
- Increased readable and maintenance
  - Nesting small class within top-level class can place code closer and precise

### Features
accessing fields of enclosing class even private
static X (only through an object reference just like any other top-level class)
inner O

> Static Nested-class is behaviorally a top-level class that has been nested in another top-level class for packaging convenience

> Top-level static class is not exist in java

define static method or field itself
static O
inner X

> Because an inner class is associated with an instance, it cannot define any static members itself.

declared location
static class-level
inner class-level


#### Local Class
This is special kind of Inner Class.

declared location
local in-a-block (typically method body)

accessing instance fields of enclosing class even private
local defined in method O
local defined in static-method X

accessing class fields of enclosing class
local defined in method X
local defined in static-method O

accessing local variable or parameter of enclosing class
local O (local variable that is defined `final` or `effectively final` since java 8)

> Local class is subset of Inner class, It means that It can't define static field but constant variables. Because constant typically declared `static final` will be transformed to each code snippet in compile-time.

#### Anonymous Class
This is special kind of Inner Class.


### What is difference
`Inner class require instance of outer class for initialization` and they are always associated with instance of enclosing class. On the other hand nested static class is `not associated with any instance of enclosing class`.

### 1. Static nested classes
It can only access current static method or variable of Outer class.

```java
OuterClass.StaticNestedClass nestedObject = new OuterClass.StaticNestedClass();
```

### 2. Non-static nested (Inner) classes
It is possible to invoke methods on the enclosing instance.

```java
OuterClass.InnerClass innerObject = outerObject.new InnerClass();
```

```java
class A
{
    class B
    {
        // static int x; not allowed here
    }

    static class C
    {
        static int x; // allowed here
    }
}

class Test
{
    public static void main(String… str)
    {
        A a = new A();

        // Non-Static nested (Inner) Class
        // Requires enclosing instance
        A.B obj1 = a.new B();

        // Static nested Class
        // No need for reference of object to the outer class
        A.C obj2 = new A.C();
    }
}
```
