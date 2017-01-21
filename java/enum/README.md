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
 - http://stackoverflow.com/questions/6667243/using-enum-values-as-string-literals
```

### Enums with value 1.
```java
public enum Planet {
    MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    ;

    private final double mass;   // in kilograms
    private final double radius; // in meters

    private Planet(double mass, double radius) {  // private constructor
        this.mass = mass;
        this.radius = radius;
    }
    private double mass() { return mass; }
    private double radius() { return radius; }
}
```

> **The constructor** for an enum type must be package-private or **private access**. It automatically creates the constants that are defined at the beginning of the enum body. You cannot invoke an enum constructor yourself.

### Enum with values 2.
```java
public final class Mode {
  // public static final fields
  public static final String MODE_1 = "Mode 1";
  public static final String MODE_2 = "Mode 2";
  public static final String MODE_3 = "Mode 3";

  private Mode() {}
}
```

### Enum with values 3. (recommended)
```java
public enum Mode {
  MODE_1, // equals to MODE_1("MODE_1")
  MODE_2, // equals to MODE_2("MODE_2")
  ;
}

// enum to string
String name = Mode.MODE_1.name();
// string to enum
Mode mode = Mode.valueOf(name);
```
> every single enum has **name()**, **valueOf()** method in default.

### Enum Integration
```java
for (Direction dir : Direction.values()) {
  // do what you want
}
```
> **.values()** returns an array containing the constants of this enum type

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
