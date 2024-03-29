## Jackson

```
@author: suktae.choi
- https://www.mkyong.com/java/jackson-2-convert-java-object-to-from-json/
- http://www.baeldung.com/jackson-annotations
- https://www.mkyong.com/java/java-convert-object-to-map-example/
```

### Blog
- [Annotations](http://www.baeldung.com/jackson-annotations)
- [MixIn](https://github.com/FasterXML/jackson-docs/wiki/JacksonMixInAnnotations): Entity 수정없이 선택적으로 필드 포함/제외

### Serialize
#### to JSON
```java
// to jsonString
String result = mapper.writeValueAsString(new Person());
```

#### to Map
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

#### from Super-Type-Token
```java
// TypeFactory (== JavaType)
JavaType type = mapper.getTypeFactory.constructCollectionType(List.class, SomeClass.class);
List<SomeClass> someClassList = mapper.readValue(jsonString, type);

// TypeReference
List<SomeClass> list = mapper.readValue(jsonString, new TypeReference<List<SomeClass>>() {});
```

- TypeFactory
  - dynamic: can be diverse in runtime
  - no anonymous instance is created
- TypeReference
  - static: must be known in compile-time
  - anonymous instance is created: new TypeReference() {}
