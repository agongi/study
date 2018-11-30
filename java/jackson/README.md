## Jackson

```
ㅁ Author: suktae.choi
ㅁ References:
 - https://www.mkyong.com/java/jackson-2-convert-java-object-to-from-json/
 - http://www.baeldung.com/jackson-annotations
 - https://www.mkyong.com/java/java-convert-object-to-map-example/
```

### [Annotations](http://www.baeldung.com/jackson-annotations)

### Serialize
#### to JSON
```java
// to jsonString
String result = mapper.writeValueAsString(new Person());
```

### to Map
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class JacksonTest {

    @Builder
    @Getter
    @Setter
    private static class TestObject {
        private String name;
        private int age;
        private List<String> friends;
    }

    @Test
    public void object_to_map() {
        ObjectMapper objectMapper = new ObjectMapper();

        TestObject object = TestObject.builder()
                .name("name")
                .age(46)
                .friends(Arrays.asList("A", "B"))
                .build();

        Map<String, Object> bindMap = objectMapper.convertValue(object, Map.class);

        log.debug("bindMap: {}", bindMap);
    }
}
```

### Deserialize
#### from Object
```java
String jsonString = "{\"name\":\"suktae\"}";

// from object
Person person = mapper.readValue(jsonString, Person.class);
```

#### from Collection<Object>
```java
// TypeFactory (== JavaType)
JavaType type = mapper.getTypeFactory.constructCollectionType(List.class, SomeClass.class);
List<SomeClass> someClassList = mapper.readValue(jsonString, type);

// TypeReference
List<SomeClass> list = mapper.readValue(jsonString, new TypeReference<List<SomeClass>>() {});
```

- TypeFactory
  - dynamic: can be diverse in runtime
- TypeReference
  - static: must be known in compile-time
