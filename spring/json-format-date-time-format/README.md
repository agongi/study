# @JsonFormat vs @DateTimeFormat
```
https://stackoverflow.com/questions/15164864/how-to-accept-date-params-in-a-get-request-to-spring-mvc-controller
https://stackoverflow.com/questions/37871033/spring-datetimeformat-configuration-for-java-time
https://jojoldu.tistory.com/361?category=635883
```

## @JsonFormat
응답/요청 Body 에 정의한 필드 format 을 명세합니다:
```java
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
```java
// @ResponseBody
@Data
public class ResponseVO {
    private String name;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = DatePatterns.DATETIME_SYSTEM_COMPACT, timezone = "Asia/Seoul")
    private Date regDate;
}
```

## @DateTimeFormat
요청 파라미터에 정의한 필드 format 을 명세합니다:
- @DateTimeFormat 을 응답필드에 정의할 수 없음 (JSON 응답이므로)
```java
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
```java
// @RequestParam
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
public void getById(
    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss") @RequestParam("regDate") LocalDateTime regDate,
    @DateTimeFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss") @RequestParam("modDate") Date modDate {
    
    // ...
}
```