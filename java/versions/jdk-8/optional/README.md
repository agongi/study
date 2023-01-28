## Optional

```
ㅁ Author: suktae.choi
- http://blog.eomdev.com/java/2016/04/01/%EC%9E%90%EB%B0%948%EC%9D%98-java.time-%ED%8C%A8%ED%82%A4%EC%A7%80.html
- http://d2.naver.com/helloworld/645609
```

### Nutshell
- Optional.of()
  - not null
- Optional.ofNullable()
  - nullable
- Optional.flatMap()
- Optional.map()
- Optional.empty()

p.333 java 8 in action


#### Optional to Stream
```java
// java.util
public String getUrl() {
     return Optional.ofNullable(requests) // Optional<List<Request>>
         .map(Collection::stream)         // Stream<List<Request>>
         .orElse(Stream.empty())          // ..
         .findFirst()                     // Stream<Request>
         .map(Request::getUrl)            // Stream<String>
         .orElse(null);                   // String
}
// apache.commons
public String getUrl() {
    return CollectionUtils.emptyIfNull(representImages)
        .stream()                         // Stream<List<Request>>
        .findFirst()                      // Stream<Request>
        .map(Request::getImageUrl)        // Stream<String>
        .orElse(null);                    // String
}
```

#### flatMap()
```java
// nested Map<String, List<String>>
public List<String> getUrl() {
    return Optional.ofNullable(RequestString)   
        .map(o -> JacksonUtils.toObject(o, Request.class))
        .map(Request::getHeaderMap)       // Optional<Map<String, List<String>>>
        .map(o -> o.values().stream())    // Optional<List<List<String>>> -> Stream<List<List<String>>>
        .orElse(Stream.empty())           // ..
        .flatMap(List::stream)            // Stream<List<String>>
        .collect(Collectors.toList());    // List<String>
}
```

#### Array to Stream
```java
Optional.ofNullable(jsonString)                     // Optional<String>   
    .map(o -> StringUtils.split(o.trim(), ",\r\n")) // Optional<String[]>
    .map(Arrays::stream)                            // Stream<List<String>>
    .orElse(Stream.empty())                         // ...
    .collect(Collectors.toSet());                   // Set<String>
```
