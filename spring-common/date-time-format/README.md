## @DateTimeFormat

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.09
ㅁ References:
 - https://stackoverflow.com/questions/15164864/how-to-accept-date-params-in-a-get-request-to-spring-mvc-controller
```

### DateTime param in controller
```java
@RequestMapping(value="/users" , method=RequestMethod.GET)
public ModelAndView getUsers(
  @RequestParam(value = "since", required = false) @DateTimeFormat(iso = DateTimeFormat.ISO.DATE_TIME) DateTime since) {
    // ...
  }
```

### Timestamp in controller
```java
@RequestMapping(value="/users" , method=RequestMethod.GET)
public ModelAndView getUsers(
  @RequestParam(value = "since", required = false) Long since {
    // ...
  }

// service
DateTime sinceValue = new DateTime(since.longValue());
```
