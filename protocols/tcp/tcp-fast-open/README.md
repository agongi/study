## TCP Fast Open (TFO)
TCP Fast Open (TFO) is an extension to speed up the opening of TCP three-way-handshake between two endpoints.

```
@author: suktae.choi
- https://en.wikipedia.org/wiki/TCP_Fast_Open
- http://knight76.tistory.com/entry/%EA%B5%AC%EA%B8%80-TCP-Fast-Open-paper-TFO%EB%A5%BC-%EC%9D%BD%EA%B3%A0
```

<img src="images/Screen%20Shot%202017-09-02%20at%2014.55.03.png" width="75%">

Client stored cookie after initial connection and reuse it later along with sending SYN packet. If cookie is valid, the server may start sending data even before three-way-handshake is in progress.

1. First TCP Connection request (called, cold request) initiates sending SYN along with **TFO cookie**
2. Normal three-way-handshake is going on while client saves given TFO cookie for future usage
3. Client requests TCP connection again with TFO cookie
4. Server accepts SYN request along with validating TFO cookie
5. Normal three-way-handshake is in progress **while server sends packets to client** once TFO cookie is valid

> It skips a round-trip delay(RTT) and lowering the latency in the start of data transmission.

> Cold request costs **8 - 28%** more than a request over existing connection.
