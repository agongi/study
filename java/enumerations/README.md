## Java Enumerations
Java fundamental of Enumerations.

> It gives you `type safety` and `self-documenting`.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.12
ㅁ References:
 - http://stackoverflow.com/questions/11575376/why-use-enums-instead-of-constants
 - http://stackoverflow.com/questions/2229297/java-enumerations-vs-static-constants
 - http://www.nextree.co.kr/p11686/
 - http://www.mkyong.com/java/java-enum-example/
 - https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html
 - http://stackoverflow.com/questions/4709175/what-are-enums-and-why-are-they-useful
```

### Enums with value
```java
public enum Planet {
    MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    EARTH   (5.976e+24, 6.37814e6),
    MARS    (6.421e+23, 3.3972e6),
    JUPITER (1.9e+27,   7.1492e7),
    SATURN  (5.688e+26, 6.0268e7),
    URANUS  (8.686e+25, 2.5559e7),
    NEPTUNE (1.024e+26, 2.4746e7);

    private final double mass;   // in kilograms
    private final double radius; // in meters

    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }
    private double mass() { return mass; }
    private double radius() { return radius; }
}
```

> **The constructor** for an enum type must be package-private or **private access**. It automatically creates the constants that are defined at the beginning of the enum body. You cannot invoke an enum constructor yourself.

### Type Safety
```java
public enum Fruit {
  APPLE, MELON
}
public enum Person {
  FEMALE, MALE
}
public class Constants {
  public static final String MALE = "Male";
  public static final String FEMALE = "Female";
  public static final String APPLE = "Apple";
  public static final String MELON = "Melon";
}


/* Correct */
Fruit fruit = Fruit.APPLE;
Person person = Person.MALE;

/* Incorrect - Compile-time error: incompatible types! */
Fruit fruit = Person.MALE;
Person person = Fruit.APPLE;

/* Incorrect - Potentially-Runtime error: incompatible types! */
String fruit = Constants.MALE;
String person = Constants.APPLE;
```

### equivalent
```java
public enum Color {
  RED,
  AMBER,
  GREEN
}

public class Color {
    private Color() {} // Prevent others from making colors.
    public static final Color RED = new Color();
    public static final Color AMBER = new Color();
    public static final Color GREEN = new Color();

    public static void main() {
      assertTrue(Color.RED, Color.GREEN); // fail
    }
}
```

```java
public enum Color {
  RED("aaa"),
  AMBER("bbb"),
  GREEN("ccc"),
  ;

  private String value;
  private Color(String value) {
    this.value = value;
  }
  public String value() {
    return this.value;
  }
}

public class Color {
    // Prevent others from making colors.
    private Color(String value) {
      this.value = value;
    }
    public static final Color RED = new Color("aaa");
    public static final Color AMBER = new Color("bbb");
    public static final Color GREEN = new Color("ccc");

    private final String value;
    public String value() {
      return this.value;
    }

    public static void main() {
      assertTrue(Color.RED.value(), "aaa");  // succeed
    }
}
```
