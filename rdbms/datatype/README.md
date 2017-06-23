## Data Type

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.23
ㅁ References:
 - https://zetawiki.com/wiki/MySQL_%EC%9E%90%EB%A3%8C%ED%98%95
 - https://stackoverflow.com/questions/144283/what-is-the-difference-between-varchar-and-nvarchar
 - https://zetawiki.com/wiki/MySQL_datetime,_timestamp_%EC%B0%A8%EC%9D%B4
```

### Index
The most common used:
- tinyint
- int
- bigint

- datetime

- varchar

### char vs varchar
Use varchar to use storage efficiently

- char
  - fixed-length: This will occupy the remain space
- varchar
  - variable-length: This will not reserve the remain space

> nchar & nvarchar support Unicode and need 2-times more space.

### datetime vs timestamp
Use **datetime**

- datetime
  - 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59

- timestamp
  - 1970-01-01 00:00:00 ~ 2037-12-31 23:59:59

### Overflow int
Convert to **bigint**

- int
  - (signed) -2147483648 ~ 2147483647

- bigint
  - (signed) -9223372036854775808 ~ 9223372036854775807

> define int(32) like change length doesn't work
