## Java 8
TDB

> ddd
```
ㅁ Author: suktae.choi
ㅁ Date: 2016.11.12
ㅁ Origin:
 - Functional Programming in Java 8
ㅁ References:
 - TBD
```

### Flow that could be transformed
```
String <===> byte[] <===> ByteBuffer (non-direct)
```

### terms
- capacity
 - physical(actual) buffer size
- limit
 - limitation(logical) of buffer size
- position
 - current starting point of read/write
 - mark
  - set specific position for

```
2016-10-30 23:33:27 [main] [DEBUG] com.games.io.ByteBufferTest - java.nio.HeapByteBuffer [pos=0 lim=6 cap=6]
```
- put()/get()
 - basic in/out I/O commands
- flip()
 - limit = pos, pot = 0

 > use case: put data and ready to get

- clear()
 - pos = 0, limit = capacity

 > It doesn't delete data in buffer, just set position to 0. (clear all meta-data)

- reset()
 - jump pos to mark (pos = mark)
- remaining()
 - position에서 limit까지의 공간

#### String to byte[]
```java
// String to byte[]
byte[] bytes = "suktae".getBytes();
log.debug(bytes.toString());
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - [B@1c2c22f3
```

#### byte[] to ByeBuffer
```java
// byte[] to ByteBuffer
ByteBuffer buf = ByteBuffer.wrap(bytes);
log.debug(buf.toString());
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - java.nio.HeapByteBuffer[pos=0 lim=6 cap=6]
```

#### ByeBuffer to byte[]
```java
// ByteBuffer to byte[]
byte[] arr = new byte[buf.capacity()];
buf.get(arr);
log.debug(arr.toString());
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - [B@4c75cab9
```

#### byte[] to String
```java
// byte[] to String
String result = new String(arr);
log.debug(result);
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - suktae
```
