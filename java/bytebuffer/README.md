## Java ByteBuffer
Java quick-reference of handling ByteBuffer.

> **String** `can be converted to Byte[]` provided in java.nio package; **ByteBuffer** `is the wrapper of byte[]` for easy using, providing better method for handling I/O operations.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.25
ㅁ References:
 - http://zhe-thoughts.github.io/2016/01/05/Java-array-ByteBuffer/
 - https://examples.javacodegeeks.com/core-java/nio/bytebuffer/convert-between-bytebuffer-and-byte-array/
 - http://eincs.com/2009/08/java-nio-bytebuffer-channel/
 - http://aoruqjfu.fun25.co.kr/index.php/post/567
 - https://www.mkyong.com/java/how-do-convert-byte-array-to-string-in-java/
```

### Flow that could be transformed
```
String <===> byte[] <===> ByteBuffer
```

### String to byte[]
```java
// String to byte[]
byte[] bytes = "suktae".getBytes();
log.debug(bytes.toString());
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - [B@1c2c22f3
```

### byte[] to ByeBuffer
```java
// byte[] to ByteBuffer
ByteBuffer buf = ByteBuffer.wrap(bytes);
log.debug(buf.toString());
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - java.nio.HeapByteBuffer[pos=0 lim=6 cap=6]
```

### ByeBuffer to byte[]
```java
// ByteBuffer to byte[]
byte[] arr = new byte[buf.capacity()];
buf.get(arr);
log.debug(arr.toString());
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - [B@4c75cab9
```

### byte[] to String
```java
// byte[] to String
String result = new String(arr);
log.debug(result);
```
```
2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - suktae
```