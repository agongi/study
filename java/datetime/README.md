## Datetime

```
ㅁ Author: suktae.choi
ㅁ References:
- http://blog.eomdev.com/java/2016/04/01/%EC%9E%90%EB%B0%948%EC%9D%98-java.time-%ED%8C%A8%ED%82%A4%EC%A7%80.html
- http://d2.naver.com/helloworld/645609
```

### Timestamp vs Instant

\#toString 시 zone 의 유무만 다르고 정확하게 동일한 시간대를 의미

```java
@Test
public void timeStampAndInstantTest() {
  Instant instant = Instant.now();
  Timestamp timestamp = Timestamp.from(instant);

  log.info("instant={}", instant);
  log.info("timestamp={}", timestamp);

  assertEquals(instant.toEpochMilli(), timestamp.getTime());
}

// console
instant=2020-03-15T13:16:37.688885Z
timestamp=2020-03-15 22:16:37.688885
```

### java.sql.Date vs java.util.Date

- java.util.Date
  - Represents Date + Time
  - **nanos truncated**
- java.sql.Date **extends java.util.Date**
  - Represent Date
  - Equivalent DB `date` type
- java.sql.Time **extends java.util.Date**
  - Represents Time
  - Equivalent DB `time` type
  - **nanos truncated**
- java.sql.Timestamp **extends java.util.Date**
  - Represents Date + Time
  - Equivalent DB `timestamp` type
  - **nanos included**

### Usage

```java
@Test
public void localDateTimeTest() {
  // #now
  assertNotNull(LocalDateTime.now());
  // #of
  assertNotNull(LocalDateTime.of(2020, 03, 15, 23, 59));

  // String -> LocalDateTime
  LocalDateTime dateTime = LocalDateTime.parse("202003152359", DateTimeFormatter.ofPattern("yyyyMMddHHmm"));
  // LocalDateTime -> String
  assertEquals(dateTime.format(DateTimeFormatter.ISO_DATE_TIME), "2020-03-15T23:59:00");
  assertEquals(dateTime.format(DateTimeFormatter.ISO_DATE), "2020-03-15");

  // plus, minus
  assertEquals(dateTime.plusDays(1).toString(), "2020-03-16T23:59");
  assertEquals(dateTime.minusYears(1).toString(), "2019-03-15T23:59");

  // compare
  assertTrue(dateTime.isBefore(dateTime.plusDays(1)));
  assertTrue(dateTime.isAfter(dateTime.minusYears(1)));
}

@Test
public void localDateTest() {
  // #now
  assertNotNull(LocalDate.now());
  // #of
  assertNotNull(LocalDate.of(2020, 03, 15));

  // String -> LocalDate
  LocalDate date = LocalDate.parse("202003152359", DateTimeFormatter.ofPattern("yyyyMMddHHmm"));
  // LocalDate -> String
  assertEquals(date.format(DateTimeFormatter.ISO_DATE), "2020-03-15");

  // plus, minus
  assertEquals(date.plusDays(1).toString(), "2020-03-16");
  assertEquals(date.minusYears(1).toString(), "2019-03-15");

  // compare
  assertTrue(date.isBefore(date.plusDays(1)));
  assertTrue(date.isAfter(date.minusYears(1)));
}

@Test
public void zonedDateTimeTest() {
  // #now
  assertNotNull(ZonedDateTime.now());
  // #of
  assertNotNull(ZonedDateTime.of(LocalDateTime.now(), ZoneId.of("Asia/Seoul")));

  // String -> LocalDateTime
  ZonedDateTime dateTime = ZonedDateTime.parse("202003152359+09:00", DateTimeFormatter.ofPattern("yyyyMMddHHmmz"));
  // LocalDateTime -> String
  assertEquals(dateTime.format(DateTimeFormatter.ISO_DATE_TIME), "2020-03-15T23:59:00+09:00");
  assertEquals(dateTime.format(DateTimeFormatter.ISO_DATE), "2020-03-15+09:00");

  // plus, minus
  assertEquals(dateTime.plusDays(1).toString(), "2020-03-16T23:59+09:00");
  assertEquals(dateTime.minusYears(1).toString(), "2019-03-15T23:59+09:00");

  // compare
  assertTrue(dateTime.isBefore(dateTime.plusDays(1)));
  assertTrue(dateTime.isAfter(dateTime.minusYears(1)));
}

@Test
public void instantTest() {
  // #now
  assertNotNull(Instant.now());
  // #of
  assertNotNull(Instant.ofEpochMilli(1584284340000L));

  // String -> Instant
  Instant instant = Instant.parse("2020-03-15T14:59:00Z");
  // Instant -> String
  assertEquals(instant.toEpochMilli(), 1584284340000L);
  assertEquals(instant.toString(), "2020-03-15T14:59:00Z");
}
```

***

Prior to JDK 8

### Convert

#### Date - String
```java
// date to string
Date date = new Date();
String dateString = DateFormatUtils.format(date, "yyyy-MM-dd");

////////////////////////////////////////////////////////////////////

// string to date
String now = "2018-10-01";
Date date = DateUtils.parseDate(now, "yyyy-MM-dd");
```

### Binding

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
