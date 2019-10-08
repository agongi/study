## @DateTimeFormat

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/15164864/how-to-accept-date-params-in-a-get-request-to-spring-mvc-controller
- https://stackoverflow.com/questions/37871033/spring-datetimeformat-configuration-for-java-time
- https://jojoldu.tistory.com/361?category=635883
```

### Request

#### JsonFormat

@RequestBody

```java
@RequestMapping(value = "/{id}", method = RequestMethod.POST)
public void getById(
    @RequestBody RequestVO request,
	BindingResult bindingResult) {
    
    // ...
}

// @RequestBody
@Data
public class RequestVO {
    private String name;
	
    // It is also working
    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime regDate;
    
	@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = DatePatterns.DATETIME_SYSTEM_COMPACT, timezone = "Asia/Seoul")
    private Date modDate;
}
```

#### DateTimeFormat

@ModelAttribute

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
public void getById(RequestVO request) {
    // ...
}


// @ModelAttribute
@Data
public class RequestVO {
    private String name;

    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime regDate;
    
    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private Date modDate;
}
```

@RequestParam

```java
// @RequestParam
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
public void getById(
    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss") @RequestParam("regDate") LocalDateTime regDate,
    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss") @RequestParam("modDate") Date modDate {
    
    // ...
}
```

### Response

#### JsonFormat

@ResponseBody

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
public ResponseVO getById(@PathVariable("id") String id) {
    // ...
}

// @ResponseBody
@Data
public class ResponseVO {
    private String name;

	@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = DatePatterns.DATETIME_SYSTEM_COMPACT, timezone = "Asia/Seoul")
	private Date regDate;
}
```

#### DateTimeFormat

Not working!

> Jackson only cares POJO from/to json