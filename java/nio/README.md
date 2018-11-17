## NIO (New Input/Output)

```
ㅁ Author: suktae.choi
ㅁ References:
- http://palpit.tistory.com/640
- https://javapapers.com/java/java-nio-file-read-write-with-channels/
```

### Index
- [ByteBuffer](bytebuffer)
- [Files](files)

### 1. Channel vs Stream
stream is directional (1-way)
 : read Stream
 : write Stream

channel is bi-directional (2-way)
 : read/write channel

### 2. Buffer vs Stream
stream is the model of realtime transfer
 : 1 byte in, 1 byte out

buffer is the mechanism of storing a mount of data before transfer
 : 1 byte in, more bytes comes then send

 > Buffer has more performance benefit rather than stream.

### 3. File I/O
https://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/

https://homoefficio.github.io/2016/08/13/%EB%8C%80%EC%9A%A9%EB%9F%89-%ED%8C%8C%EC%9D%BC%EC%9D%84-AsynchronousFileChannel%EB%A1%9C-%EB%8B%A4%EB%A4%84%EB%B3%B4%EA%B8%B0/


### 4. TCP (Connection-Oriented) Blocking Channel
**Server**
 - open()
 - bind()
 - accept()

> Blocked until connect() is coming from client

 - read()

> Blocked until write(buffer) is coming from client.  

 - write()

> Used to answer the request to client.

**Client**
 - open()
 - connect()
 - write()
 - read()

> Blocked until write(buffer) is coming from server. Normally the answer of request from client.

**Architect**
one thread per request -> any thread in `threadPool` per request with `queue`.

> Server resource issues

<img src="images/Screen%20Shot%202016-05-15%20at%2016.43.17.png" width="75%">

<img src="images/Screen%20Shot%202016-05-15%20at%2016.43.12.png" width="75%">

### 5. TCP (Connection-Oriented) Non-Blocking Channel
selector

### 6. UDP
**Server**​
 - open()
 - bind()​
 - ~~accept()~~
 - receive()

> Blocked until send(buffer) is coming from client.  

 - send()

**Client**
 - open()
 - ~~connect()~~
 - send()

> UDP originally works on connection-less mechanism. so there is no write/read for transmission. but you are also able to use connect() to use read/write functions in UDP. but actually no handshaking happens in client/server side.
