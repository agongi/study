## Static Nested Class vs Non-static Nested Class (Inner Class)
A **non-static nested** class has `full access to the members of the class within` which it is nested. A **static nested class** does not have a reference to a nesting instance, so a static nested class `cannot invoke non-static methods or access non-static fields of an instance of the class within` which it is nested.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ Origin: Effective Java 2nd
ㅁ References:
 - http://javarevisited.blogspot.kr/2012/12/inner-class-and-nested-static-class-in-java-difference.html
 - http://stackoverflow.com/questions/70324/java-inner-class-and-static-nested-class
 - http://stackoverflow.com/questions/1353309/java-static-vs-non-static-inner-class
 - http://docs.oracle.com/javase/tutorial/java/javaOO/nested.html
```

#### When to use
Any class which is `only used by its outer class`

#### What is difference
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
