## MessageFormat.format() vs String.format()

```
ㅁ Author: suktae.choi
ㅁ References:
- https://stackoverflow.com/questions/2809633/difference-between-messageformat-format-and-string-format-in-jdk1-5
```

#### MessageFormat.format()
```java
@Test
public void messageFormatTest() {
    // static method format create instance
    log.info(MessageFormat.format("hello {0}, world! {1}", 3, 5));

    // reuse object
    MessageFormat format = new MessageFormat("hello {0}, world! {1}");
    log.info(format.format(new String[] {"3", "5"}));
}
```

#### String.format()
```java
@Test
public void stringFormatTest() {
    log.info(String.format("hello %s, world %d", "3", 5));
}
```

> performance? MessageFormat is much better than String.format in my cases
