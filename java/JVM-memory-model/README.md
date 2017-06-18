## JVM Memory Model


```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.18
ㅁ References:
 - http://www.nextree.co.kr/p3878/
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

### [Jackson Annotations](http://www.baeldung.com/jackson-annotations)
