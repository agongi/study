## Java 8 Stream

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.07.25
ㅁ References:
 - http://d2.naver.com/helloworld/4911107
 - http://iloveulhj.github.io/posts/java/java-stream-api.html
 - https://www.mkyong.com/java8/java-8-flatmap-example/
```

<img src="https://github.com/agongi/study/blob/master/java/stream/images/figure2.jpg" width="75%">

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
    // ...
```

#### distinct()
```java
// remove duplicated
list.stream()
    .distinct()
    // ...
```

#### reduce()


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
// instance in orElse() is made in advance regardless the result of Optional<T> - cost inefficient
list.stream().filter(name -> name.getId() != 0).findFirst().orElse(null);
list.stream().filter(name -> name.getId() != 0).findFirst().orElse(new Name());

// this is good practice, simple and no instance is made
list.stream().filter(o -> o > 5).findFirst().orElse(0);
```

#### orElseGet()
```java
// get if not null, return default one defined in orElse() if null
// lazy-loaded method if null only - cost efficient
list.stream().filter(name -> name.getId() != 0).findFirst().orElseGet(() -> new Name());
list.stream().filter(name -> name.getId() != 0).findFirst().orElseGet(Name::new);
```

#### orElseThrow()
```java
list.stream().filter(name -> name.getId() != 0).findFirst().orElseThrow(() -> new IllegalArgumentException());
list.stream().filter(name -> name.getId() != 0).findFirst().orElseThrow(IllegalArgumentException::new);
```

#### Optional.ofNullable(obj);
```java
Optional.ofNullable(list).orElse(new Name(10, "haha"));
```

### Collectors
#### Collectors.toMap(Function::identity())
```java
Map<Integer, Object> map = list.stream()
    .collect(Collectors.toMap(Object::getId, Function::identity()));
```

#### Collectors.groupingBy()
```java
Map<Integer, List<Object>> map = list.stream()
    .collect(Collectors.groupingBy(Object::getId));
```

#### Collectors.summingInt()
```java
Map<Integer, Integer> map = list.stream()
    .collect(Collectors.groupingBy(Object::getId, Collectors.summingInt(Object::getScore)));
```

#### Collectors.joining()
```java
List<String> list = Arrays.asList("java", "python", "nodejs", "ruby");

// java | python | nodejs | ruby
String result = list.stream().map(x -> x).collect(Collectors.joining(" | "));
```

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
