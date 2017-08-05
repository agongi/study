## Java 8 Stream

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.07.25
ㅁ References:
 - http://d2.naver.com/helloworld/4911107
 - http://www.slideshare.net/madvirus/8-35205661
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
스트림들을 하나의 스트림으로 합쳐서 하나의 새로운 스트림을 반환


#### filter()
... 필터링 하는거

#### distinct()
중복제거

#### limit
숫자 제한

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
// instance is made whether null or not - cost inefficient
list.stream().filter(name -> name.getId() != 0).findFirst().orElse(null);
list.stream().filter(name -> name.getId() != 0).findFirst().orElse(new Name());

// this is good practice, simple and no instance is made
list.stream().filter(o -> o > 5).findFirst().orElse(0);
```

#### orElseGet()
```java
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

#### Collectors.toMap(Object::getKey, Function::identity())
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
