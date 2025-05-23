# Enum
```
https://mkyong.com/java/java-enum-example/
https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html
https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom
https://javarevisited.blogspot.kr/2012/09/what-is-enummap-in-java-example-tutorial.html
```

## [Nested Class Holder](https://en.wikipedia.org/wiki/Initialization-on-demand_holder_idiom)
ENUM 내부에 정보를 가진 Holder 클래스를 두고, 생성자에서 초기화/기능제공 하는 패턴
```java
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
        
        // nested holder (생성시점에 static 클래스 초기화)
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

## Static Holder
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

## Abstract Methods
```java
@Getter
@Setter
@RequiredArgsConstructor
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

## Implements Interface
```java
public interface Price {
    double getPrice();
}

@Getter
@RequiredArgsConstructor
public enum Books implements Price {
    HARRY_POTTER (12.99),
    THE_SOULFORGE (12.11),
    GAME_OF_THRONES (10.00),
    DRAGONLANCE (6.77);

    private final double price;
}
```

## Enum Group
```java
interface EnumGroup<E extends Enum<E>> {
    EnumSet<E> getGroup();
}

@Getter
@RequiredArgsConstructor
public enum AlphabetType {
    A("A", 1),
    B("B", 2),
    C("C", 3),
    X("X", 24);

    private String name;
    private Integer id;


    @Getter
    @RequiredArgsConstructor
    private enum Group implements EnumGroup<AlphabetType> {
        HEAD(EnumSet.of(A, B, C)),
        TAIL(EnumSet.of(X));

        private EnumSet group;
    }
}

public static void main(String[] args) {
    Set<AlphabetType> set = AlphabetType.Group.HEAD.getGroup();
    log.info("group.HEAD: {}", set);
}
```

## EnumSet, EnumMap
EnumSet, EnumMap are much efficient of handling enumType data
```java
@Test
public void testSetMap() {
    Map<TestType, String> map = new EnumMap<>(TestType.class);

    Set<TestType> set = EnumSet.allOf(TestType.class);
}
```
