# Jackson
```
https://www.mkyong.com/java/jackson-2-convert-java-object-to-from-json/
https://www.baeldung.com/jackson-annotations
https://www.mkyong.com/java/java-convert-object-to-map-example/
```
### Blog
- [Annotations](http://www.baeldung.com/jackson-annotations)
- [MixIn](https://github.com/FasterXML/jackson-docs/wiki/JacksonMixInAnnotations): Entity 수정없이 선택적으로 필드 포함/제외
***
## Serialize
### to JSON
```java
// to jsonString
String result = mapper.writeValueAsString(new Person());
```

### to Map
```java
@Slf4j
public class JacksonTest {
    @Builder
    @Getter
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

## Deserialize
### from Object
```java
String jsonString = "{\"name\":\"suktae\"}";

// from object
Person person = mapper.readValue(jsonString, Person.class);
```

### from Super-Type-Token
Generic 은 빌드타임에 타입이 결정되어 런타임에 타입을 알 수 없습니다.

> \<> 은 빌드시점에 형변환되어 컴파일됨

그래서 런타임에 해당 Generic 을 유지하기 위해 타입토큰을 사용 합니다 (Generic 정보 보관 목적)


```java
// TypeReference
List<SomeClass> list = mapper.readValue(jsonString, new TypeReference<List<SomeClass>>() {});
```
