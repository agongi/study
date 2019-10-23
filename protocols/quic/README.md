## QUIC
```
ㅁ Author: suktae.choi
ㅁ References:
- http://www.slideshare.net/Jxck/spdy-http2-quic-bpstudy-20130828
- https://evan-moon.github.io/2019/10/08/what-is-http3/
```

QUIC is an experimental protocol aimed at reducing web latency over that of TCP. On the surface, QUIC is very similar to TCP+TLS+SPDY implemented on UDP. Because TCP is implement in operating system kernels, and middlebox firmware, making significant changes to TCP is next to impossible. However, since QUIC is built on top of UDP, it suffers from no such limitations.

>TCP + TLS + SPDY over UDP

Key features of QUIC over existing TCP+TLS+SPDY include:

 - Dramatically reduced connection establishment time
 - Improved congestion control
 - Multiplexing without head of line blocking
 - Forward error correction
 - Connection migration

<img src="images/Screen%20Shot%202016-02-20%20at%2019.50.43.png" width="75%">

<img src="images/0rtt-graphic.png" width="75%">
