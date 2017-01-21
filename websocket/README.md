## WebSocket
The WebSocket Protocol is an `independent TCP-based protocol`. Its only relationship to `HTTP is that its handshake` is interpreted by HTTP servers as an `Upgrade request`. The communications are done over TCP port number 80 or 443, which is of benefit for those environments which block non-web Internet connections using a firewall.

>###### It is a TCP-based protocol using HTTP for handshaking to migrate into WebSocket to avoid network firewall, proxy and any other security infrastructures on the net.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.11 ~ 06.03
ㅁ References:
 - https://en.wikipedia.org/wiki/WebSocket
 - http://www.joinc.co.kr/w/man/12/websocket
 - https://dzone.com/refcardz/html5-websocket
 - https://tools.ietf.org/html/rfc6455
 - http://stackoverflow.com/questions/2681267/what-is-the-fundamental-difference-between-websockets-and-pure-tcp
 - http://stackoverflow.com/questions/19568432/why-websocket-needs-an-opening-handshake-using-http-why-cant-it-be-an-independ/19569871?noredirect=1#19569871
 - http://stackoverflow.com/questions/16945345/differences-between-tcp-sockets-and-web-sockets-one-more-time
 - http://coding.debuntu.org/c-linux-socket-programming-tcp-simple-http-client
 - http://ohgyun.com/436
 - http://www.gamedevforever.com/210
```

 - TCP over custom protocol

 : bi-directional
 : encode/decode (codec) is required in program <br>
 : payload size is restricted in buffer capacity <br>

 > So It causes program that **re-framing should be implemented** in this manner

 > while(BufferedInputStream.read()) { in.read(array); }

 - TCP over WebSocket protocol via API

 : bi-directional
 : encode/decode is done in ws API based on specification<br>
 : no barrier of payload size

 > **Re-framing is done** in WebSocket API level
 
 > whole message is transfer to remote, and event-driven mechanism calls callback to notify program to receive it

#### 1. client request -> handshake to WebSocket protocol over 80 or 443 port

```
GET ws://localhost/chat HTTP/1.1
  Host: server.example.com
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
  Origin: http://example.com
  Sec-WebSocket-Protocol: chat, superchat
  Sec-WebSocket-Version: 13
```

<img src="https://github.com/agongi/study/blob/master/websocket/images/Screen%20Shot%202016-05-11%20at%2001.17.49.png" width="75%">

#### 2. server response -> handshake done to differ to WebSocket

```
HTTP/1.1 101 Switching Protocols
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzza
```

> **Q. Why websocket needs an opening handshake using HTTP? Why can't it be an independent protocol?** <br><br>
**A.** The WebSocket Protocol attempts to address the goals of existing bidirectional HTTP technologies in the context of the **existing HTTP infrastructure**; as such, it is designed to work over HTTP ports 80 and 443 as well as to support HTTP proxies and intermediaries, even if this implies some complexity specific to the current environment. However, the design does not limit WebSocket to HTTP, and future implementations could use a simpler handshake over a dedicated port without reinventing the entire protocol.

<img src="https://github.com/agongi/study/blob/master/websocket/images/Screen%20Shot%202016-05-11%20at%2001.30.34.png" width="75%">

#### 3. bidirectional communication is going on without unnecessary HTTP headers and latency of pushing event from server to client

<img src="https://github.com/agongi/study/blob/master/websocket/images/Screen%20Shot%202016-05-11%20at%2001.30.49.png" width="50%">

> WS provides APIs to make easy communication each other in async.

> Network security is guaranteed over TLS. packet is encrypted in xor operation based on `Sec-WebSocket-Key` which is shared in handshake mechanism.

###### Benefit of WebSocket compared to HTTP

WebSocket request & response headers:

<img src="https://github.com/agongi/study/blob/master/websocket/images/WebSocketStream.png" width="75%">

 - Data transfer is done within one TCP connection lifecycle.
 - `No extra headers after handshake`. You might notice that the "length" column represents each packet's size, it is less than 100 bytes by average in my case and it only depend on exact transferred data size.

In Ajax polling or Comet, HTTP requests/responses with header information is impossible to achieve same level performance as WebSocket, both of them created new HTTP (TCP) connections to transfer data, and each connection's size is relatively larger than WebSocket, especially when there are cookies stored in header or long headers such as "User-Agent", "If-Modified-Since", "If-Match", "X-Powered-By", etc.

One thing deserves to be mentioned is the TCP keep alive signals, we should consider close the WebSocket connection as soon as we don't need it any more, otherwise bandwidth will be wasted.
