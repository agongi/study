## Java Jackson
Java quick-reference of using Jackson.

> Both are using ObjectMapper in `com.fasterxml.jackson` over version 2.x.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.14
ㅁ References:
 - https://www.mkyong.com/java/jackson-2-convert-java-object-to-from-json/
```

### Object to JSON
```java
ObjectMapper mapper = new ObjectMapper();
Person person = new Person();

// Convert Object to JSON in String
String result = mapper.writeValueAsString(person);
```

### JSON to object
```java
ObjectMapper mapper = new ObjectMapper();
String result = "{'name' : 'suktae'}";

// Convert JSON in String to Object
Person person = mapper.readValue(result, Person.class);
```
