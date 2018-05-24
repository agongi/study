## Java Enumerations

```
ㅁ Author: suktae.choi
ㅁ References:
 - http://stackoverflow.com/questions/11575376/why-use-enums-instead-of-constants
 - http://stackoverflow.com/questions/2229297/java-enumerations-vs-static-constants
 - http://www.nextree.co.kr/p11686/
 - http://www.mkyong.com/java/java-enum-example/
 - https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html
 - http://stackoverflow.com/questions/4709175/what-are-enums-and-why-are-they-useful
 - http://stackoverflow.com/questions/6667243/using-enum-values-as-string-literals
 - https://stackoverflow.com/questions/443980/why-cant-enums-constructor-access-static-fields
 - https://stackoverflow.com/questions/32337555/stream-of-vs-arrays-stream-to-get-an-enum-from-a-value
 - https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom
```

```java
// traditional
public final class Mode {
  public static final String MODE_1 = "MODE_1";
  public static final String MODE_2 = "MODE_2";
}

// plain enum
public enum Mode {
  MODE_1, // equals to MODE_1("MODE_1")
  MODE_2, // equals to MODE_2("MODE_2")
  ;
}

// reality
public enum Planet {
    MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    ;

    private final double mass;   // in kilograms
    private final double radius; // in meters

    // private constructor
    private Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }

    // getters
    public double mass() { return mass; }
    public double radius() { return radius; }
}
```

### Usage
#### From/To
```java
// enum to string
String name = Mode.MODE_1.name();
// string to enum
Mode mode = Mode.valueOf(name);
```
> Every single enum has **name()**, **valueOf()** method in default.

#### Iteration
```java
for (Direction dir : Direction.values()) {
  // do what you want
}
```
> **.values()** returns an array containing the constants of this enum type

#### Boolean Field
```java
@Getter
@AllArgsConstructor(access = AccessLevel.PRIVATE)
public enum Direction {
  YES(true),
  NO(false),
  ;

  boolean used;
}

// isXXX is auto-generated in case of boolean field
YES.isUsed() == true;
NO.isUsed() == false;
```

#### Default Value
```java
@Getter
public enum Direction {
  YES("name_yes", true),
  NO("name_no"),  // default value in field is assigned
  ;

  String name;  // mandatory
  boolean used = true;  // optional, default true

  private Direction(String name) {
    this.name = name;
  }
  private Direction(String name, boolean used) {
    this.name = name;
    this.used = used;
  }
}
```

#### [Nested Class Holder](https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom)
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class EnumTest {

    @Getter
    public enum AlphabetType {
        A("A", 1),
        B("B", 2),
        C("C", 3),
        ;

        private String name;
        private Integer id;

        AlphabetType(String name, Integer id) {
            this.name = name;
            this.id = id;
            AlphabetTypeHolder.alphabetIdMap.put(id, this);
        }

        private static class AlphabetTypeHolder {
            private static Map<Integer, AlphabetType> alphabetIdMap = new HashMap<>();
        }

        public static AlphabetType findById(Integer id) {
            return Arrays.stream(values()).filter(o -> o.getId().equals(id)).findFirst().get();
        }

        public static AlphabetType findByIdHolder(Integer id) {
            return AlphabetTypeHolder.alphabetIdMap.get(id);
        }
    }

    @Test
    public void test00_findById() {
        AlphabetType type1 = AlphabetType.findById(1);
        log.info("type1: {}", type1);

        AlphabetType type2 = AlphabetType.findByIdHolder(2);
        log.info("type2: {}", type2);
    }
}
```

#### Static Holder
enum constants translate to **public static final** fields. These appear textually first in the enum type definition and are therefore initialized first. Their initialization involves the constructor.

The rules exists to prevent the constructor from seeing uninitialized values of other class variables that will necessarily be initialized later.

```java
// runtime error
enum Color {
   RED, GREEN, BLUE;
   static final Map<String,Color> colorMap = new HashMap<String,Color>();

  Color() {
     colorMap.put(toString(), this);  // NPE
  }
}

// *************************************************************************
enum Color {
  RED, GREEN, BLUE;
  private static final Map<String,Color> colorMap;

  static {
    HashMap<String, Color> map = new HashMap<>();
    for (Color c : Color.values()) {
      colorMap.put(c.toString(), c);
    }

    colorMap = Collections.unmodifiableMap(map); // good practice
  }
}
```

> Class initialization invokes static block first, and static fields in textual order

> Enum should define enum values at top in class

#### Abstract Methods
```java
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public static class AlphabetVO {
    private String name;
    private Integer id;
}

@Getter
public enum AlphabetType {
    A("A", 1) {
        @Override
        public AlphabetVO getAlphabet() {
            return new AlphabetVO(getName(), getId());
        }
    },
    B("B", 2) {
        @Override
        public AlphabetVO getAlphabet() {
            return new AlphabetVO(getName(), getId());
        }
    },
    C("C", 3) {
        @Override
        public AlphabetVO getAlphabet() {
            return new AlphabetVO(getName(), getId());
        }
    },
    ;

    private String name;
    private Integer id;

    abstract public AlphabetVO getAlphabet();
}
```

#### Implements Interface
```java
public interface Price {
    public double getPrice();
}

@Getter
@AllArgsConstructor
public enum Books implements Price {
    HARRY_POTTER (12.99),
    THE_SOULFORGE (12.11),
    GAME_OF_THRONES (10.00),
    DRAGONLANCE (6.77);

    private final double price;
}
```

#### Enum Group
```java
interface EnumGroup<E extends Enum<E>> {
    EnumSet<E> getGroup();
}

@Getter
@AllArgsConstructor
public enum AlphabetType {
    A("A", 1),
    B("B", 2),
    C("C", 3),
    X("X", 24);

    private String name;
    private Integer id;


    @Getter
    @AllArgsConstructor
    private enum Group implements EnumGroup<AlphabetType> {
        HEAD(EnumSet.of(A, B, C)),
        TAIL(EnumSet.of(X));

        private EnumSet group;
    }
}

public static void main(String[] args) {
    EnumSet<AlphabetType> set = AlphabetType.Group.HEAD.getGroup();
    log.info("group.HEAD: {}", set);
}
```

#### EnumSet, EnumMap
EnumSet, EnumMap are much efficient of handling enumType data
```java
@Test
public void testSetMap() {
    Map<TestType, String> map = new EnumMap<>(TestType.class);

    Set<TestType> set = EnumSet.allOf(TestType.class);
}
```
