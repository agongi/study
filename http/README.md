## HTTP
HTTP is world-wide web protocol that is used all browser and web-related client and server. Recently new spec of HTTP/2 has published so this section will cover what is main differences among each of HTTP versions and SPDY.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.01
ㅁ References:
 - http://www.slideshare.net/Jxck/spdy-http2-quic-bpstudy-20130828
 - http://stackoverflow.com/questions/246859/http-1-0-vs-1-1
 - https://libosong.appspot.com/spdy/index.html
 - https://http2.github.io/faq
 - https://en.wikipedia.org/wiki/Head-of-line_blocking
 - https://www.nginx.com/blog/http-keepalives-and-web-performance/
 - http://d2.naver.com/helloworld/140351
 - https://developers.google.com/web/fundamentals/performance/http2/#design_and_technical_goals
```

### 1. HTTP/1.0
The First official protocol in 1996. (RFC 1945) It supports almost current HTTP features.

### 2. HTTP/1.1
HTTP/1.1 is updated version of HTTP/1.0 in 1999. (RFC 2068) It has some improvement.

#### Host field
HTTP header `Host` is mandatory in HTTP/1.1 but optional in 1.0.<br>
It is useful when proxy needs to route request to proper WAS server by referencing `Host` field.

```
GET / HTTP/1.1
Host: www.google.com
```

#### Keep-alive / Persistent Connections
HTTP/1.1 supports `Keep-alive` to re-use the TCP session by default but header `Connection: keep-alive` should be declared explicitly in HTTP/1.0.

> TCP session needs to be negotiated in 3-way-handshake. That is expensive.

#### N-TCP-Connection
Modern browser (e.g. Firefox, Chrome, Edge ..) is able to create 2-TCP-Connections per domain to get over performance limitation. The total number of connections is 4 to 8 based on browser type/version.

Browser makes a trick to expand maximum number of connection to separate subdomain e.g. 2-Connections per image.google.com, 2-Connections per video.google.com.

<img src="https://github.com/agongi/study/blob/master/http/images/Screen%20Shot%202017-04-11%20at%2001.39.28.png" width="75%">

#### Pipelining
HTTP/1.1 supports `Pipelining` to enable fast resource response but not in HTTP/1.0.
> Pipelining enables client to send all request in parallel before receives response. Server responses packet `in the same order` that the requests were received.

> It causes [Head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking) problem. (Output is occupied by first packet in line.)

<img src="https://github.com/agongi/study/blob/master/http/images/Zp2lf.png" width="75%">

#### CORS (Cross Origin Resource Sharing)
HTTP/1.1 introduces the `OPTIONS method`.

### 3. HTTP/2
SPDY is invented by Google to improve HTTP/1.1 flaws. The core developers of SPDY have been involved in the development of HTTP/2, including both Mike Belshe and Roberto Peon. As of February 2015, Google has announced that following the recent final ratification of the HTTP/2 standard, support for `SPDY would be deprecated`, and that support for SPDY would be withdrawn completely in 2016.

#### Binary framing layer
<img src="https://github.com/agongi/study/blob/master/http/images/Screen%20Shot%202017-04-11%20at%2001.44.59.png" width="75%">

The `layer` refers to a design choice to introduce a new optimized encoding mechanism between the socket interface and the higher HTTP API exposed to our applications: the HTTP semantics, such as verbs, methods, and headers, are unaffected, but the way they are encoded while in transit is different. Unlike the **newline delimited plaintext** HTTP/1.x protocol, all HTTP/2 communication is split into smaller **messages** and **frames**, each of which is encoded in binary format.

As a result, both client and server must use the new binary encoding mechanism to understand each other:

- Stream: A bidirectional flow of bytes within an established connection, which may carry one or more messages.
- Message: A complete sequence of frames that map to a logical request or response message.
- Frame: The smallest unit of communication in HTTP/2, each containing a frame header, which at a minimum identifies the stream to which the frame belongs.

<img src="https://github.com/agongi/study/blob/master/http/images/Screen%20Shot%202017-04-11%20at%2001.37.17.png" width="75%">

The relation of these terms can be summarized as follows:

- All communication is performed over a single TCP connection that can carry any number of bidirectional streams.
- Each stream has a unique identifier and optional priority information that is used to carry bidirectional messages.
- Each message is a logical HTTP message, such as a request, or response, which consists of one or more frames.
- The frame is the smallest unit of communication that carries a specific type of data—e.g., HTTP headers, message payload, and so on. Frames from different streams may be interleaved and then reassembled via the embedded stream identifier in the header of each frame.


#### One-TCP-Connection
With HTTP/1.x, browsers open between 4 and 8 connections per origin. This may improve performance in parallel situation but each of connections need to be negotiated called `3-way-handshake` that cause `RTT (Round-Trip-Time)` and reduce performance.

One TCP Connection means that client need to negotiate 3-way-handshake once and use it multiply.

#### Multiplexing
The new binary framing layer in HTTP/2 resolves the head-of-line blocking problem found in HTTP/1.x and eliminates the need for multiple connections to enable parallel processing and delivery of requests and responses.

- Interleave multiple requests in parallel without blocking on any one.
- Interleave multiple responses in parallel without blocking on any one.
- Use a single connection to deliver multiple requests and responses in parallel.

> Multiplexing enables client to send all request in parallel before receives response. Server responses packet `out-of-order` that the requests were received.

> Priority bit can be sent in request is able to send response `out-of-order` based on request priority too.

<img src="https://github.com/agongi/study/blob/master/http/images/Screen%20Shot%202017-04-11%20at%2001.37.08.png" width="75%">

#### Header Compression
HTTP header is compressed using `HPACK`. It significantly reduces HTTP response size by compression and improves performance almost 30%.

#### Server Push
Server can push static resources e.g. css and/or javascript **BEFORE** respond HTML request.

Pushed resources are cached in browser and `cache-hit (HTTP 304 Not Modified)`.

<img src="https://github.com/agongi/study/blob/master/http/images/Screen%20Shot%202016-02-02%20at%2000.34.17.png" width="75%">

#### Priority
Client could `set priority` in request so when server receives it, It can set up priority in processing and response it rather than others.
