## Comparable and/or Comparator
 This will show the use of `java.lang.Comparable` and `java.util.Comparator` to sort a Java object based on its property value.

>A class can implement Comparable to override compareTo method, this allows class being sorted in one way. But create Comparator can be adopted in any use-cases as a module.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.07.27
ㅁ References:
 - https://examples.javacodegeeks.com/core-java/util/comparator/java-comparator-example/
 - https://www.mkyong.com/java/java-object-sorting-example-comparable-and-comparator/
```

### 1. Sort an Array

```java
String[] fruits = new String[] {"Pineapple","Apple", "Orange", "Banana"};

Arrays.sort(fruits);

int i=0;
for(String fruit : fruits) {
	System.out.println("fruits " + ++i + " : " + fruit);
}
```

```
fruits 1 : Apple
fruits 2 : Banana
fruits 3 : Orange
fruits 4 : Pineapple
```

### 2. Sort an ArrayList

```java
List<String> fruits = new ArrayList<String>();

fruits.add("Pineapple");
fruits.add("Apple");
fruits.add("Orange");
fruits.add("Banana");

Collections.sort(fruits);

int i=0;
for(String fruit : fruits) {
	System.out.println("fruits " + ++i + " : " + fruit);
}
```

```
fruits 1 : Apple
fruits 2 : Banana
fruits 3 : Orange
fruits 4 : Pineapple
```

### 3. Sort an Object with Comparable

```java
/**
 * @author suktae.choi
 */
public class Fruit {

    private String fruitName;
    private String fruitDesc;
    private int quantity;

    public Fruit(String fruitName, String fruitDesc, int quantity) {
        this.fruitName = fruitName;
        this.fruitDesc = fruitDesc;
        this.quantity = quantity;
    }

    public String getFruitName() {
        return fruitName;
    }
    public void setFruitName(String fruitName) {
        this.fruitName = fruitName;
    }
    public String getFruitDesc() {
        return fruitDesc;
    }
    public void setFruitDesc(String fruitDesc) {
        this.fruitDesc = fruitDesc;
    }
    public int getQuantity() {
        return quantity;
    }
    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }
}
```

```java
import java.util.Arrays;

/**
 * @author suktae.choi
 */
public class Main {

    public static void main(String args[]) {

        Fruit[] fruits = new Fruit[4];

        Fruit pineAppale = new Fruit("Pineapple", "Pineapple description", 70);
        Fruit apple = new Fruit("Apple", "Apple description", 100);
        Fruit orange = new Fruit("Orange", "Orange description", 80);
        Fruit banana = new Fruit("Banana", "Banana description", 90);

        fruits[0] = pineAppale;
        fruits[1] = apple;
        fruits[2] = orange;
        fruits[3] = banana;

        Arrays.sort(fruits);

        int i = 0;
        for(Fruit fruit : fruits) {
            System.out.println("fruits "
                    + ++i
                    + " : "
                    + fruit.getFruitName()
                    + ", Quantity : " + fruit.getQuantity());
        }
    }
}
```

```
Exception in thread "main" java.lang.ClassCastException: Fruit cannot be cast to java.lang.Comparable
	at java.util.ComparableTimSort.countRunAndMakeAscending(ComparableTimSort.java:320)
	at java.util.ComparableTimSort.sort(ComparableTimSort.java:188)
	at java.util.Arrays.sort(Arrays.java:1246)
	at Main.main(Main.java:22)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:497)
	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147)

Process finished with exit code 1
```

It needs to implement Comparable interface to let JDK knows how to sort a object based on which values.

```java
public class Fruit implements Comparable<Fruit> {

    private String fruitName;
    private String fruitDesc;
    private int quantity;

    public Fruit(String fruitName, String fruitDesc, int quantity) {
        super();
        this.fruitName = fruitName;
        this.fruitDesc = fruitDesc;
        this.quantity = quantity;
    }

    public String getFruitName() {
        return fruitName;
    }
    public void setFruitName(String fruitName) {
        this.fruitName = fruitName;
    }
    public String getFruitDesc() {
        return fruitDesc;
    }
    public void setFruitDesc(String fruitDesc) {
        this.fruitDesc = fruitDesc;
    }
    public int getQuantity() {
        return quantity;
    }
    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }

    @Override
    public int compareTo(Fruit o) {
        /* ascending order */
        return this.quantity - o.getQuantity();
    }
}
```

### 4. Sort an Object with Comparator

```java
package main.utils;

import main.model.Fruit;

import java.util.Comparator;

/**
 * @Author suktaechoi
 */
public class FruitQuantityComparator implements Comparator<Fruit> {

    @Override
    public int compare(Fruit o1, Fruit o2) {
        return o1.getQuantity() - o2.getQuantity();
    }
}
```

```java
package main.utils;

import main.model.Fruit;

import java.util.Comparator;

/**
 * @Author suktaechoi
 */
public class FruitNameComparator implements Comparator<Fruit> {

    @Override
    public int compare(Fruit o1, Fruit o2) {
        return o1.getFruitName().compareTo(o2.getFruitName());
    }
}
```

```java
package main;

import main.model.Fruit;
import main.utils.FruitNameComparator;
import main.utils.FruitQuantityComparator;

import java.util.Arrays;

/**
 * @author suktae.choi
 */
public class Main {

    public static void main(String args[]) {

        Fruit[] fruits = new Fruit[4];

        Fruit pineAppale = new Fruit("Pineapple", "Pineapple description", 70);
        Fruit apple = new Fruit("Apple", "Apple description", 100);
        Fruit orange = new Fruit("Orange", "Orange description", 80);
        Fruit banana = new Fruit("Banana", "Banana description", 90);

        fruits[0] = pineAppale;
        fruits[1] = apple;
        fruits[2] = orange;
        fruits[3] = banana;

        /* sort based on fruitName with custom comparator */
        Arrays.sort(fruits, new FruitNameComparator());
        print(fruits);

        /* sort based on fruitQuantity with custom comparator */
        Arrays.sort(fruits, new FruitQuantityComparator());
        print(fruits);
    }

    private static void print(Fruit[] fruits) {
        int i = 0;
        for(Fruit fruit : fruits) {
            System.out.println("fruits "
                    + ++i
                    + " : "
                    + fruit.getFruitName()
                    + ", Quantity : " + fruit.getQuantity());
        }

        System.out.println();
    }
}
```

```
fruits 1 : Apple, Quantity : 100
fruits 2 : Banana, Quantity : 90
fruits 3 : Orange, Quantity : 80
fruits 4 : Pineapple, Quantity : 70

fruits 1 : Pineapple, Quantity : 70
fruits 2 : Orange, Quantity : 80
fruits 3 : Banana, Quantity : 90
fruits 4 : Apple, Quantity : 100

Process finished with exit code 0
```
