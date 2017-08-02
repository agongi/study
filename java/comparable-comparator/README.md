## Comparable vs Comparator
Comparable interface overrides compareTo method in a class. Comparator interface can be defined **separately and used as module**.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.07.27
ㅁ References:
 - https://examples.javacodegeeks.com/core-java/util/comparator/java-comparator-example/
 - https://www.mkyong.com/java/java-object-sorting-example-comparable-and-comparator/
```

### Comparable
```java
/**
 * @author suktae.choi
 */
@Getter
@Setter
@Slf4j
@ToString
public class Fruit implements Comparable<Fruit> {

    private String fruitName;
    private String fruitDesc;
    private int quantity;

    public Fruit(String fruitName, String fruitDesc, int quantity) {
        this.fruitName = fruitName;
        this.fruitDesc = fruitDesc;
        this.quantity = quantity;
    }

    @Override
    public int compareTo(Fruit o) {
        return this.quantity - o.getQuantity();
    }

    public static void main(String args[]) {

        List<Fruit> fruits = Arrays.asList(
            new Fruit("Pineapple", "Pineapple description", 70),
            new Fruit("Apple", "Apple description", 100),
            new Fruit("Orange", "Orange description", 80),
            new Fruit("Banana", "Banana description", 90)
        );

        List<Fruit> sortedFruits = fruits.stream().sorted().collect(Collectors.toList());
        for (Fruit fruit : sortedFruits) {
            log.debug("fruit: {}", fruit);
        }
    }
}
```

### Comparator
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class FruitComparator implements Comparator<Fruit> {
    @Override
    public int compare(Fruit o1, Fruit o2) {
        return o1.compareTo(o2);  // if class implements comparable
        // return o1.getQuantity() - o2.getQuantity();  // if not.
    }

    public static void main(String[] args) {
        List<Fruit> fruits = Arrays.asList(
                new Fruit("Pineapple", "Pineapple description", 70),
                new Fruit("Apple", "Apple description", 100),
                new Fruit("Orange", "Orange description", 80),
                new Fruit("Banana", "Banana description", 90)
        );

        List<Fruit> sortedFruits = fruits.stream().sorted(new FruitComparator()).collect(Collectors.toList());
        for (Fruit fruit : sortedFruits) {
            log.debug("fruit: {}", fruit);
        }
    }
}
```
