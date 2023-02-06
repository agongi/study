## WebSocket

```
@author: suktae.choi
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

#### Why WebSocket?
- comet (== polling)
  - packet size: HTTP headers + payload
  - notified: polling
- WebSocket
  - packet size: payload only (after connection)
  - notified: event-driven

<img src="images/Screen%20Shot%202016-05-11%20at%2001.30.50.png" width="75%">

### client -> server: `Upgrade` protocol handshake

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

<img src="images/Screen%20Shot%202016-05-11%20at%2001.17.49.png" width="75%">

### server -> client: 101 Switching Protocols

```
HTTP/1.1 101 Switching Protocols
  Upgrade: websocket
  Connection: Upgrade
  Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzza
```

> **Q. Why websocket needs an opening handshake using HTTP? Why can't it be an independent protocol?** <br><br>
**A.** The WebSocket Protocol attempts to address the goals of existing bidirectional HTTP technologies in the context of the **existing HTTP infrastructure**; as such, it is designed to work over HTTP ports 80 and 443 as well as to support HTTP proxies and intermediaries, even if this implies some complexity specific to the current environment.

<img src="images/Screen%20Shot%202016-05-11%20at%2001.30.34.png" width="75%">

### client <-> server: communication bidirectionally

<img src="images/Screen%20Shot%202016-05-11%20at%2001.30.49.png" width="60%">

> HTTP headers are not transferred after connection

> Network security is guaranteed over TLS. packet is encrypted in xor operation based on `Sec-WebSocket-Key` which is shared in handshake mechanism.
