## ByteBuffer

```
@author: suktae.choi
- http://eincs.com/2009/08/java-nio-bytebuffer-channel/
- http://aoruqjfu.fun25.co.kr/index.php/post/567
- https://www.mkyong.com/java/how-do-convert-byte-array-to-string-in-java/
- http://a07274.tistory.com/281
- http://darksilber.tistory.com/entry/ByteBuffer-%EB%B0%94%EC%9D%B4%ED%8A%B8%EB%B2%84%ED%8D%BC
```

### Cores

#### Direct vs Non-direct
- Non-direct
  - system_call()
  - I/O (device controller)
  - load to kernel
  - **copy to heap**
    - use CPU
    - occupy JVM memory
  - CRUD in heap memory
  - **GC clear heap in future**
    - cause GC
- Direct
  - system_call()
  - I/O (device controller)
  - load to kernel
    - DiskController cares without CPU (when it's done, send CPU signal) -> DMA
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

#### Terms

- mark - save point
- position - current position
- limit - logical endpoint
- capacity - physical endpoint

```
0 <= mark <= position <= limit <= capacity
```

### Basic Operations

#### Position

- \#flip: `limit` 을 `position` 위치로 세팅후, 처음으로 돌아감
  - limit = position
  - position = 0

>현재까지 읽은만큼 다시 읽을때

- \#rewind: 처음으로 돌아감
  - position = 0

> replay

- \#clear: 초기화
  - position = 0
  - limit = capacity (Initialize)
- \#reset: `position`을 `mark` 위치로 세팅
  - position = mark
- \#remaining/\#hasRemaining: `limit` 까지 남은 bytes 계산
  - limit - position
- \#compact: `position` ~ `limit` 까지의 데이터를 buf 처음으로 복사, `position`을 복사한 데이터 바로 다음 위치로 이동 & `limit`는 `capacity`로 이동

> 잔여 (안읽은) data 는 buf 처음으로 복사하고, 나머지는 capacity 만큼 다시 채워서 처리할때

#### CRUD

- \#put(byte)
- \#get

> ((DirectBuffer)directBuffer).cleaner().clean() 을 이용해서 명시적으로 DirectBuffer clear 도 가능하다.

### Convert
#### String to byte[]

```java
// String to byte[]
byte[] bytes = "suktae".getBytes();
log.debug(bytes.toString());

// 2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - [B@1c2c22f3]
```
#### byte[] to ByeBuffer
```java
// byte[] to ByteBuffer
ByteBuffer buf = ByteBuffer.wrap(bytes);
log.debug(buf.toString());

// 2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - java.nio.HeapByteBuffer[pos=0 lim=6 cap=6]
```
#### ByeBuffer to byte[]
```java
// ByteBuffer to byte[]
byte[] arr = new byte[buf.capacity()];
buf.get(arr);
log.debug(arr.toString());

// 2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - [B@4c75cab9
```
#### byte[] to String
```java
// byte[] to String
String result = new String(arr);
log.debug(result);

// 2016-10-24 23:48:46 [main] [DEBUG] com.games.Application - suktae
```