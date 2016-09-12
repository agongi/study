## Overriding vs Overloading
Java conceptual comparison between Overriding and Overloading.

>###### Override method is selected run-time but Overload is in compile-time, @Override is normally in our purpose.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.28
ㅁ Origin: Effective Java 2nd
```

#### 1. Overriding
 - @Override is `redefined` the method of interface or super class.
 - Function name, parameters, return type ... All are the same.
 - Method is selected in `run-time`.

```java
public class CollectionClassifier {
  public static String name(Set<?> set) { return "Set"; }
  public static String name(List<?> list) { return "List"; }
  public static String name(Collections<?> collection) { return "Unknown"; }

  public static void main(String[] args) {
    Collection<?> collections = {
      new HashSet<?>(),
      new ArrayList<?>(),
      new Stack<?>()
    }

    for(Collection collection : collections) {
      // method is selected in compile-time. It doesn't meet polymorphism.
      System.out.println(collection.name());
    }
  }
}

============================================================
Unknown
Unknown
Unknown
```

#### 2. Overloading
 - Overload is `added` more method of interface or super class.
 - Function name should be the same, It doesn't matter of return type but `at least one of parameter type or the number of parameters` should be different.
 - Method is selected in `compile-time`.

```java
public class Wine {
  public String name() { return "Wine"; }
}

public class WineA {
  @Override
  public String name() { return "WineA"; }
}

public class WineB {
  @Override
  public String name() { return "WineB"; }
}

public class Demo {
  public static void main(String[] args) {
    Wine[] wines = {new Wine(), new WineA(), new WineB()};

    for(Wine wine : wines) {
      // method is selected in run-time. This enables polymorphism.
      System.out.println(wine.name());
    }
  }
}

============================================================
Wine
WineA
WineB
```
