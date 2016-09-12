## Java Varargs
Java fundamental of Varargs.

>###### Varargs is equal to Array internally based on JDK. Varargs causes create array instance in each invocation.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.14
ㅁ References:
 - https://en.wikipedia.org/wiki/Hash_table
```

**When to use Varargs**
 - You don't know how many arguments you need to pass to a method?
 - You want to pass unlimited variables to a method?

```java
public class Demo {
  public static void print(String... strings) {
    // print arguments
  }

  public static void main(String[] args) {
    Demo.print("A", "B", "C");
  }
}
```

**How Varagrs works actually**

Complier automatically handles Varagrs in following :

```java
public static void print(String... strings) {
  // print arguments
}

converted into -->

public static void print(String[] strings) {
  // print arguments
}
```

```java
public static void main(String[] args) {
  Demo.print("A", "B", "C");
}

converted into -->

public static void main(String[] args) {
  Demo.print(new String[]{"A", "B", "C"});
}
```
