## Static Nested Class vs Non-Static Nested Class (Inner Class)
Java conceptual comparison between Nested Static and Inner Class.

> If a class can only be useful to one particular class, it make sense to keep that inside the class itself  otherwise declare it as top level class.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ Origin: Effective Java 2nd
ㅁ References:
 - http://javarevisited.blogspot.kr/2012/12/inner-class-and-nested-static-class-in-java-difference.html
```

**When to use? : <br>**
simple answer is any class which is `only used by its outer class`, should be a good candidate of making inner

**What is difference? : <br>**
First and most important difference between Inner class and nested static class is that `Inner class require instance of outer class for initialization` and they are always associated with instance of enclosing class. On the other hand nested static class is `not associated with any instance of enclosing class`.

#### 1. Inner class
You can access current instance of Outer class, inside inner class as Outer.this variable.

> In order to create instance of Inner class, an instance of outer class is required. Every instance of inner class is bounded to one instance of Outer class.

###### 1.1. Local inner class
Local inner class is declared inside a code block or method.<br>
It can not be private, protected or public because they exist only inside of local block or method. You can only use final modifier with local inner class.

###### 1.2. Anonymous inner class
Anonymous inner class is a class which doesn't have name to reference and initialized at same place where it gets created.<br>
This is commonly used to implement Runnable or CommandListener where you just need to implement one method. They are created and initialized at same line.

###### 1.3. Member inner class
Member inner class is declared as non static member of outer class.<br>
This can be make private, protected or public. its just like any other member of class.



```java
public class InnerClassTest {

    public static void main(String args[]) {

        //creating local inner class inside method
        class Local {
            public void name() {
                System.out.println("Example of Local class in Java");
            }
        }

        //creating instance of local inner class
        Local local = new Local();
        local.name(); //calling method from local inner class

        //Creating anonymous inner class in java for implementing thread
        Thread anonymous = new Thread(){
            @Override
            public void run(){
                System.out.println("Anonymous class example in java");
            }
        };
        anonymous.start();

        //example of creating instance of inner class
        InnerClassTest test = new InnerClassTest();
        InnerClassTest.Inner inner = test.new Inner();
        inner.name(); //calling method of inner class
    }

    /*
     * Creating Inner class in Java
     */
    private class Inner{
        public void name(){
            System.out.println("Inner class example in java");
        }
    }
}

Output:
Example of Local class in Java
Inner class example in java
Anonymous class example in java
```

#### 2. Nested static class
You can access current static method or variable of Outer class.
> Instance of nested static class is not attached to any enclosing instance of Outer class. You also don't need any instance of Outer class to create instance of nested static class
