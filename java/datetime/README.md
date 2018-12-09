## Datetime

```
ㅁ Author: suktae.choi
ㅁ References:
 - http://blog.eomdev.com/java/2016/04/01/%EC%9E%90%EB%B0%948%EC%9D%98-java.time-%ED%8C%A8%ED%82%A4%EC%A7%80.html
 - http://d2.naver.com/helloworld/645609
```

### Convert
#### Date <-> String
```java
// date to string
Date date = new Date();
String dateString = DateFormatUtils.format(date, "yyyy-MM-dd");

////////////////////////////////////////////////////////////////////

// string to date
String now = "2018-10-01";
Date date = DateUtils.parseDate(now, "yyyy-MM-dd");
```

#### Bind
```java
// DB to Entity
@DateTimeFormat("yyyy-MM-dd")
private String dateString;

// API to VO
@JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyyMMddHHmmss", timezone="Asia/Seoul")
private Date regDate;
```

### Operation
#### Modification
```java
Date date = new Date();

// set year/month/day
date = DateUtils.setYears(date, 2018);
// add (plus,minus) year/month/day
date = DateUtils.addMonths(date, -1);
// truncate (20181130121212) to 20181100000000
date = DateUtils.truncate(date, Calendar.MONTH); // truncate
```

#### Comparison
```java
Date date1 = new Date();
date1 = DateUtils.addMonths(date1, 1);

Date date2 = new Date();

boolean isAfter = date1.after(date2);
log.debug("isAfter: {}", isAfter);

boolean isBefore = date1.before(date2);
log.debug("isBefore: {}", isBefore);

boolean isSame = DateUtils.isSameDay(date1, date2);
log.debug("isSame: {}", isSame);
```
