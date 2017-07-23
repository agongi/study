## Jackson
Java quick-reference of using Jackson.

> Both are using ObjectMapper in `com.fasterxml.jackson` over version 2.x.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.14
ㅁ References:
 - https://www.mkyong.com/java/jackson-2-convert-java-object-to-from-json/
 - http://www.baeldung.com/jackson-annotations
 - https://www.mkyong.com/java/java-convert-object-to-map-example/
```

### Object to JSON
```java
ObjectMapper mapper = new ObjectMapper();
Person person = new Person();

// Convert Object to JSON in String
String result = mapper.writeValueAsString(person);
```

### JSON to Object
```java
ObjectMapper mapper = new ObjectMapper();
String result = "{'name' : 'suktae'}";

// Convert JSON in String to Object
Person person = mapper.readValue(result, Person.class);
```

### Object to Map
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

### [Jackson Annotations](http://www.baeldung.com/jackson-annotations)
