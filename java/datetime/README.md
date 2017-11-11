## Datetime

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.05.12
ㅁ References:
 - http://blog.eomdev.com/java/2016/04/01/%EC%9E%90%EB%B0%948%EC%9D%98-java.time-%ED%8C%A8%ED%82%A4%EC%A7%80.html
 - http://d2.naver.com/helloworld/645609
```

### ToString(), ToDate()
```java
// ToString()
Date date = new Date();
String dateString = DateFormatUtils.format(date, "yyyy-MM-dd");
log.debug("dateString: {}", dateString);

////////////////////////////////////////////////////////////////////

// toDate()
String now = "2018-10-01";
Date date = DateUtils.parseDate(now, "yyyy-MM-dd");

log.debug("date: {}", date);

////////////////////////////////////////////////////////////////////

// toString() with Spring support
@DateTimeFormat("yyyy-MM-dd")
private String dateString;
```

### DateUtils
- commons.lang3
```java
Date date = new Date();
date = DateUtils.setYears(date, 2018); // set
date = DateUtils.addMonths(date, -1); // plus, minus
date = DateUtils.truncate(date, Calendar.MONTH); // truncate
```

### Comparison
```java
Date date1 = new Date();
date1 = DateUtils.setYears(date1, 2018);

Date date2 = new Date();

boolean isAfter = date1.after(date2);
log.debug("isAfter: {}", isAfter);

boolean isBefore = date1.before(date2);
log.debug("isBefore: {}", isBefore);

boolean isSame = DateUtils.isSameDay(date1, date2);
log.debug("isSame: {}", isSame);
```
