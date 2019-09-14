## NIO (New Input/Output)

```
ㅁ Author: suktae.choi
ㅁ References:
- http://palpit.tistory.com/640
- https://javapapers.com/java/java-nio-file-read-write-with-channels/
- http://eincs.com/2009/08/java-nio-bytebuffer-channel/
```

#### Index
- [ByteBuffer](bytebuffer)
- [Files](files)
- Paths

### Cores

#### Channel vs Stream

- stream is directional (1-way)
  - read Stream
  - write Stream
- channel is bi-directional (2-way)
  - read/write channel

#### Buffer vs Stream
- stream is the model of realtime transfer
  - 1 byte in, 1 byte out (no buffering)
- buffer is the mechanism of storing a mount of data before transfer
  - 1 byte in, more bytes comes then send

#### TCP (Connection-Oriented) Blocking Channel
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

#### UDP
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
