## NIO (New Input/Output)

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.15
ㅁ References:
 - http://palpit.tistory.com/644
 - http://palpit.tistory.com/645
 - http://palpit.tistory.com/646
```

### Index
- [ByteBuffer](bytebuffer)




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
Path

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

<img src="https://github.com/agongi/study/blob/master/java/nio/images/Screen%20Shot%202016-05-15%20at%2016.43.17.png" width="75%">

<img src="https://github.com/agongi/study/blob/master/java/nio/images/Screen%20Shot%202016-05-15%20at%2016.43.12.png" width="75%">

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
