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
// header
HttpHeaders headers = new HttpHeaders();
headers.setContentType(MediaType.APPLICATION_JSON);
// body
MultiValueMap<String, Object> bodyMap = new LinkedMultiValueMap();
bodyMap.set("key", "value");

// requestEntity
HttpEntity request = new HttpEntity(bodyMap, headers);
// HttpEntity request = new HttpEntity(userVO, headers);  // or just use POJO object

HttpEntity<String> response = RestTemplate.exchange(
    "http://example.com",
    HttpMethod.GET, // any methods
    request,
    String.class    // if dont care of response
);
```
#### execute
It can use customized request/response callback

### Exception Handle
```java
try {
    return restTemplate.postForObject(
        url,
        URLEncoder.encode(mapper.writeValueAsString(requestVO), StandardCharsets.UTF_8.name()),
        ResponseVO.class);

} catch (RestClientResponseException e) {
    // 4xx, 5xx http status
    log.error("status: {}, body: {}", e.getRawStatusCode(), e.getResponseBodyAsString());

} catch (Exception e) {
    // invocation error
    throw new RuntimeException(e);
}
```
