## ByteBuffer

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.10.25
ㅁ References:
 - http://eincs.com/2009/08/java-nio-bytebuffer-channel/
 - http://aoruqjfu.fun25.co.kr/index.php/post/567
 - https://www.mkyong.com/java/how-do-convert-byte-array-to-string-in-java/
 - http://a07274.tistory.com/281
 - http://darksilber.tistory.com/entry/ByteBuffer-%EB%B0%94%EC%9D%B4%ED%8A%B8%EB%B2%84%ED%8D%BC
```

### Terms
- mark - save point
- position - current position
- limit - logical endpoint
- capacity - physical endpoint

```
0 <= mark <= position <= limit <= capacity
```

### Direct vs Non-direct
- Non-direct
  - system_call()
  - I/O
  - load to kernel
  - **copy to heap**
  - CRUD in heap memory
  - **GC clear heap in future**
- Direct
  - system_call()
  - I/O
  - load to kernel
  - CRUD in kernel memory

<img src="images/Screen%20Shot%202017-12-12%20at%2002.41.18.png" width="75%">

```java
// heap access (Non-direct)
ByteBuffer buff = ByteBuffer.allocate(10);
// kernel access (Direct)
ByteBuffer directBuff = ByteBuffer.allocateDirect(10);
```

> ByteBuffer only can access kernel buffer

<img src="images/Screen%20Shot%202017-12-12%20at%2001.42.43.png" width="75%">


### Basic Operations
- put()/get()
- flip()
  - limit = pos, pos = 0
- clear()
  - pos = 0, limit = capacity (Initialize)
- reset()
  - pos = mark (Jump position to mark)
- remaining()
  - limit - position

### Convert
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
