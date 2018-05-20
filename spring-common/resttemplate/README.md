## RestTemplate

```
ㅁ Author: suktae.choi
ㅁ References:
- http://blog.saltfactory.net/using-resttemplate-in-spring
- http://cakas.tistory.com/9
```

### Methods
Use default HTTP Header settings

#### GET
- getForObject()
- getForEntity()

```java
// getForEntity()
ResponseEntity<String> entity = restTemplate.getForEntity("http://example.com", String.class);
String body = entity.getBody();
MediaType contentType = entity.getHeaders().getContentType();
HttpStatus statusCode = entity.getStatusCode();

// getForObject()
String body = restTemplate.getForObject("http://example.com", String.class);
```

#### POST
- postForObject()
- postForEntity()
- postForLocation()

```java
// postForLocation()
User user = new User();
URI location = restTemplate.postForLocation("http://example.com", user, "1234");
```

#### DELETE/PUT
- delete()
- put()

```java
// put()
restTemplate.put("http://example.com", "1234");
// delete()
restTemplate.delete("http://example.com", "1234");
```

### Commons
Use customized HTTP Header for each request

#### exchange
```java
// headers
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);
// bodys
MultiValueMap<String, Object> bodyMap = new LinkedMultiValueMap();
bodyMap.set("key", "value");
// requestEntity
HttpEntity request = new HttpEntity(bodyMap, headers);

HttpEntity<String> response = RestTemplate.exchange(
    "http://example.com",
    HttpMethod.GET,       // any methods
    request,
    String.class
);
```
#### execute
It can use customized request/response callback
