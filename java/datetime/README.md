## Datetime

```
ㅁ Author: suktae.choi
ㅁ References:
- http://blog.eomdev.com/java/2016/04/01/%EC%9E%90%EB%B0%948%EC%9D%98-java.time-%ED%8C%A8%ED%82%A4%EC%A7%80.html
- http://d2.naver.com/helloworld/645609
```

## LocalDate

Instant 를 이용한 conversion 은 아래와 같습니다.

```java
// to
localDate.atStartOfDay(ZoneId.systemDefault()).toInstant();

// to java.util.Date
LocalDate localDate = LocalDate.now();
Date.from(localDate.atStartOfDay(ZoneId.systemDefault()).toInstant());

// from java.util.Date
Date date = new Date();
date.toInstant().atZone(ZoneId.systemDefault()).toLocalDate();
```

## LocalDateTime

- now
- from
  - parse
  - of
  - ofInstant
- to
  - format
  - toInstant

```java
@Test
public void localDateTimeTest() {
  // 1. now
  assertNotNull(LocalDateTime.now());
  // 2. of
  assertNotNull(LocalDateTime.of(2020, 03, 15, 23, 59));

  // 3. from
  LocalDateTime.parse("202003152359", DateTimeFormatter.ofPattern("yyyyMMddHHmm"));
  // 4. to
  dateTime.format(DateTimeFormatter.ISO_DATE_TIME);	// 2020-03-15T23:59:00
  dateTime.format(DateTimeFormatter.ISO_DATE);	// 2020-03-15

  // plus, minus
  dateTime.plusDays(1).toString();	// 2020-03-16T23:59
  dateTime.minusYears(1).toString();	// 2019-03-15T23:59

  // compare
  dateTime.isBefore(dateTime.plusDays(1));	// true
  dateTime.isAfter(dateTime.minusYears(1));	// true
}
```

Instant 를 이용한 conversion 은 아래와 같습니다.

```java
// to
localDateTime.toInstant(ZoneOffset.of("+09:00"));

// from java.util.Date
Date date = new Date();
LocalDateTime.ofInstant(date.toInstant(), ZoneId.systemDefault());

// to java.util.Date
LocalDateTime localDateTime = LocalDateTime.now();
Date.from(localDateTime.toInstant(ZoneOffset.ofHours(9)));
```

## ZonedDateTime

- now
- from
  - parse
  - of
  - ofInstant
- to
  - format
  - toInstant

```java
@Test
public void zonedDateTimeTest() {
  // 1. now
  assertNotNull(ZonedDateTime.now());
  // 2. of
  assertNotNull(ZonedDateTime.of(LocalDateTime.now(), ZoneId.of("Asia/Seoul")));

  // 3. from
  ZonedDateTime.parse("202003152359+09:00", DateTimeFormatter.ofPattern("yyyyMMddHHmmz"));
  // 4. to
	dateTime.format(DateTimeFormatter.ISO_DATE_TIME);	// 2020-03-15T23:59:00+09:00
	dateTime.format(DateTimeFormatter.ISO_DATE);	// 2020-03-15+09:00

  // plus, minus
	dateTime.plusDays(1).toString();	// 2020-03-16T23:59+09:00
	dateTime.minusYears(1).toString(); //	2019-03-15T23:59+09:00

  // compare
	dateTime.isBefore(dateTime.plusDays(1));	// true
	dateTime.isAfter(dateTime.minusYears(1));	// true
}
```

Instant 를 이용한 conversion 은 아래와 같습니다.

```java
// to
zonedDateTime.toInstant();

// from java.util.Date
Date date = new Date();
ZonedDateTime.ofInstant(date.toInstant(), ZoneId.systemDefault());

// to java.util.Date
ZonedDateTime zonedDateTime = ZonedDateTime.now();
Date.from(zonedDateTime.toInstant());
```

## Instant

```java
@Test
public void instantTest() {
  // 1. now
  assertNotNull(Instant.now());
  // 2. of
  assertNotNull(Instant.ofEpochMilli(1584284340000L));

  // 3. from
  Instant instant = Instant.parse("2020-03-15T14:59:00Z");
  // 4. to
  assertEquals(instant.toEpochMilli(), 1584284340000L);
  assertEquals(instant.toString(), "2020-03-15T14:59:00Z");
}
```

## Utility

### Duration

초/분/시 를 표현

```java
Duration duration = Duration.between(LocalDateTime.now(), LocalDateTime.now().minusHours(1));
```

### Period

일/월/년 을 표현

```java
Period period = Period.between(LocalDate.now(), LocalDate.now().minusDays(1));
```

### ZoneRegion extends ZoneId

Region 이나 UTC 기준으로 생성한다. public method 가 제공되지 않아 ZondId 를 통해 생성가능하다.

```java
ZoneId zoneId = ZoneId.of("Asia/Seoul");
ZoneId zoneIdUTC = ZoneId.of("UTC+09:00");
```

### ZoneOffset extends ZoneId

offset 을 제공해서 생성한다.

```java
ZoneId zoneOffset = ZoneOffset.of("+09:00");
```

둘의 차이점은 ZoneRules 의 유무이다. 각 region 마다 summer-time 같은 rule 이 존재 할수있는데

- ZoneOffset: Rule 미포함
- ZoneResion: Rule 포함

## 차이점

### Instant vs Timestamp

\#toString 시 zone 의 유무만 다르고 정확하게 동일한 시간대를 의미

> 대신 timestamp 는 int 라서 2038 이후의 시간을 표현하지 못함. instant 는 long

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

- Instant#toString
  - UTC+0 의 시간으로 표시
- Timestamp#toString
  - SystemDefaultZone 의 시간으로 표시 (한국에선 +09:00)

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

***

> Prior to JDK 8

## Convert

### Date - String
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
### Modification
```java
Date date = new Date();

// set year/month/day
date = DateUtils.setYears(date, 2018);
// add (plus,minus) year/month/day
date = DateUtils.addMonths(date, -1);
// truncate (20181130121212) to 20181100000000
date = DateUtils.truncate(date, Calendar.MONTH); // truncate
```

### Comparison
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
