## Java 8 Stream

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.07.25
ㅁ References:
 - http://d2.naver.com/helloworld/4911107
 - http://www.slideshare.net/madvirus/8-35205661
```

<img src="https://github.com/agongi/study/blob/master/java/stream/images/figure2.jpg" width="75%">


### map()
```java
// convert to different object list
List<Object> sortedList = list.stream()
    .map(o -> new NewObject(o.getId(), o.getScore()))
    .collect(Collectors.toList());
```

### filter()


### reduce()


### flatMap()


### sorted()
```java
List<Object> sortedList = list.stream()
    .sorted(Comparator.comparing(Object::getId))
    .collect(Collectors.toList());

// reverse order
List<Object> sortedList = list.stream()
    .sorted(Comparator.comparing(o -> o.getId(), Comparator.reverseOrder()))
    .collect(Collectors.toList());
```

### collect()
#### Collectors.toMap(Object::getKey, Function::identity())
```java
Map<Integer, Object> map = list.stream().collect(Collectors.toMap(Object::getId, Function::identity()));
```

#### Collectors.groupingBy()
```java
Map<Integer, List<Object>> map = list.stream().collect(Collectors.groupingBy(Object::getId));
```

#### Collectors.summingInt()
```java
Map<Integer, Integer> map = list.stream().collect(Collectors.groupingBy(Object::getId, Collectors.summingInt(Object::getScore)));
```

### Boolean Operation
#### findAny()

#### anyMatch()

#### allMatch()


#### isPresent()


### Return Operation
#### orElse()

#### orElseThrow()

#### get()

### Arithmetic Operation
#### mapToInt()
```java
int max = list.stream().mapToInt(Object::getId).max().getAsInt();
int min = list.stream().mapToInt(Object::getId).min().getAsInt();
```
