## Java 8 Stream

```
@author: suktae.choi
- http://d2.naver.com/helloworld/4911107
- http://iloveulhj.github.io/posts/java/java-stream-api.html
- https://www.mkyong.com/java8/java-8-flatmap-example/
```

<img src="images/figure2.jpg" width="75%">

### Stream API
#### map()
```java
// convert to different object list
List<Object> sortedList = list.stream()
    .map(o -> new NewObject(o.getId(), o.getScore()))
    .collect(Collectors.toList());
```

#### flatMap()
```java
@Slf4j
public class StreamTest {

    @Getter
    @Setter
    @AllArgsConstructor
    @ToString
    private class Name {
        private int id;
        private String name;
        private List<String> nicknames;

        public Name() {

        }
    }

    private final List<Name> list = Arrays.asList(
        new Name(1, "suktae", Arrays.asList("haha")),
        new Name(2, "yujung", Arrays.asList("hoho")),
        new Name(3, "jihyeon", Arrays.asList("hehe", "haha")),
        new Name(4, "eunho", Arrays.asList("huhu", "hihi"))
    );

    @Test
    public void test() {
      List<String> flatList = list.stream()
        .map(x -> x.getNicknames()) // { {haha}, {hoho}, {hehe, haha}, {huhu, hihi} }
        .flatMap(x -> x.stream())   // { haha, hoho, hehe, haha, huhu, hihi }
        .distinct()
        .collect(Collectors.toList());

      log.debug("flatList: {}", flatList);
    }
}
```

#### filter()
```java
// conditional statement
list.stream()
    .filter(o -> o.getId() > 0)
    // ..

// isNull
list.stream()
    .filter(Objects::isNull)
    // ..

// notNull
list.stream()
    .filter(Objects::nonNull)
    // ..
```

#### of()/concat()

```java
// of() create stream with
Stream.of("a", "b", "c")
    .filter(Objects::nonNull)
    // ...
    
// concat() merge stream with
Stream.concat(
    Arrays.asList("a", "b").stream(),
    Stream.of("c")
).collect(Collectors.toList());
```

#### distinct()

```java
// remove duplicated
list.stream()
    .distinct()
    // ...
```


#### sorted()
```java
List<Object> sortedList = list.stream()
    .sorted(Comparator.comparing(Object::getId))
    .collect(Collectors.toList());

// reverse order
List<Object> sortedList = list.stream()
    .sorted(Comparator.comparing(o -> o.getId().reversed()))
    .collect(Collectors.toList());

List<Object> sortedList = list.stream()
    .sorted(Comparator.comparing(o -> o.getId(), Comparator.reverseOrder()))
    .collect(Collectors.toList());
```

#### peek()
stream 처리도중 중간결과를 로깅할때 유효함

> map 으로 대체가능하고, 많이 사용하지않음

### Optional API
#### findAny()
```java
// - Optional<T> findAny();
list.stream().filter(o -> o.getId() > 0).findAny();
```

#### findFirst()
```java
// - Optional<T> findFirst();
list.stream().filter(o -> o.getId() > 0).findFirst();
```

#### min()
```java
// - Optional<T> min(Comparator<? super T> comparator);
list.stream().min(Comparator.comparing(Name::getId)).get();
```

#### max()
```java
// - Optional<T> max(Comparator<? super T> comparator);
list.stream().max(Comparator.comparing(Name::getId)).get();
```

### Boolean API
#### anyMatch()
```java
// - boolean anyMatch(Predicate<? super T> predicate);
list.stream().anyMatch(o -> o.getId() > 3);
```

#### allMatch()
```java
// - boolean allMatch(Predicate<? super T> predicate);
list.stream().allMatch(o -> o.getId() > 0);
```

#### noneMatch()
```java
// - boolean noneMatch(Predicate<? super T> predicate);
list.stream().noneMatch(o -> o.getId() > 0);
```

#### isPresent()
```java
// - boolean isPresent()
if(value.isPresent()) {
	value.get().someMethod();
}

// equivalent to - anti pattern
if(value != null) {
	value.someMethod();
}
```

### Return API
#### ifPresent()
```java
// execute if not null
List<Integer> results = new ArrayList<>();
list.stream().filter(o -> o.getId() > 1).findFirst().ifPresent(o -> results.add(o.getId()));

log.debug("results: {}", results);
```

#### get()
```java
// get whether null or not. Be cautious of NPE
list.stream().filter(name -> name.getId() != 0).findFirst().get();
```

#### orElse()
```java
// get if not null, return default one defined in orElse() if null
// instance is always made whether Optional<T> is null - cost inefficient
list.stream().filter(name -> name.getId() != 0).findFirst().orElse(null);
list.stream().filter(name -> name.getId() != 0).findFirst().orElse(new Name());

// this is good practice, simple and no instance is made
list.stream().filter(o -> o > 5).findFirst().orElse(0);
```

#### orElseGet()
```java
// get if not null, return default one defined in orElseGet() if null
// instance is not made if Optional<T> is null - cost efficient
list.stream().filter(name -> name.getId() != 0).findFirst().orElseGet(() -> new Name());
list.stream().filter(name -> name.getId() != 0).findFirst().orElseGet(Name::new);
```

#### orElseThrow()
```java
list.stream().filter(name -> name.getId() != 0).findFirst().orElseThrow(() -> new IllegalArgumentException());
list.stream().filter(name -> name.getId() != 0).findFirst().orElseThrow(IllegalArgumentException::new);
```

### Collectors
#### toMap() / toList() / toSet() / toCollection()
```java
Map<Integer, Object> map = list.stream()
    .collect(Collectors.toMap(Object::getId, Function::identity()));

// defines  concrete type of collection
Set<Integer> set = list.stream()
    .collect(Collectors.toCollection(HashSet::new));
```

#### collectingAndThen()
```java
@Autowired
private void setViewResolvers(List<ViewResolver> viewResolvers) {
  viewResolverMap = viewResolvers.stream()
    .collect(Collectors.collectingAndThen(
            Collectors.toMap(ViewResolver::getType, Function.identity()),
            Collections::unmodifiableMap
    ));
}
```

#### groupingBy()
```java
// key - value(groupBy)
Map<Integer, List<Object>> map = list.stream()
    .collect(Collectors.groupingBy(Object::getId));
```

#### partitioningBy()
```java
// key(boolean) - value(groupBy)
Map<Boolean, List<String>> map = list.stream()
    .collect(Collectors.partitioningBy(k -> StringUtils.length(k) > 5));
```

#### mapping()
```java
// convert U to t
// It is commonly used with groupingBy, partitioningBy
Map<City, Set<String>> lastNamesByCity = people.stream()
  .collect(groupingBy(Person::getCity, mapping(Person::getLastName, toSet())));
```

#### summingInt() / summingDouble() / summingLong()
```java
Map<Integer, Integer> map = list.stream()
    .collect(Collectors.groupingBy(Object::getId, summingInt(Object::getScore)));
```

#### minBy() / maxBy()
```java
Map<Integer, Integer> map = list.stream()
    .collect(Collectors.groupingBy(Object::getId, maxBy(comparing(Object::getScore))));
```

#### counting()
```java
Map<String, Long> map = list.stream()
    .collect(Collectors.groupingBy(Object::getId, counting()));
```

#### joining()
```java
List<String> list = Arrays.asList("java", "python", "nodejs", "ruby");

// java|python|nodejs|ruby
String result = list.stream().collect(joining("|"));
```

#### reducing()

#### comparing()

### Miscellaneous
#### mapToInt()
```java
int max = list.stream().mapToInt(Object::getId).max().getAsInt();
int min = list.stream().mapToInt(Object::getId).min().getAsInt();
```

### count()
```java
list.stream().count();
```
