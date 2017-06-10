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

### Static-Nested & Non-Static-Nested Class
Access fields of enclosing class even private
- static-nested: X
- non-static-nested: O

> Static Nested-class is behaviorally a top-level class that has been nested in another top-level class for packaging convenience

> Top-level static class is not exist in java

Access local variable or method parameter
- static-nested: X
- non-static-nested: X

> class-level declared class such as static, non-static-nested class can't have access to local variables

Define static method or field itself
- static-nested: O
- non-static-nested: X

> Because an inner class is associated with an instance, it cannot define any static members itself.

Declared location
- static-nested: class-level
- non-static-nested: class-level

#### Local & Anonymous Class
Declared location
- local & anonymous: in-a-block (typically method body)

Use more than once
- local: O
- anonymous: X

> Local class has class definition snippet in-a-block so It is available to reuse it but anonymous

Access fields of enclosing class even private
- local & anonymous defined in static method of enclosing class: X
- local & anonymous defined in method of enclosing class: O

Access class fields of enclosing class
- local & anonymous defined in static method of enclosing class: O
- local & anonymous defined in method of enclosing class: X

Access local variable or method parameter
- local & anonymous: O (local variable that is defined `final` or `effectively final` since java 8)

> Local class is subset of Inner class, It means that It can't define static field but constant variables. Because constant typically declared `static final` will be transformed to each code snippet in compile-time.

### Instantiation
```java
class A {
    class B {}
    static class C { static int x; }
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
