## TCP Head-Of-Line
TCP guarantees in-order delivery, retransmission, flow-control and congestion control. This mechanism effects all subsequent packages are held while the previous packet is lost or broken that will be retransmitted and arrives at receiver successfully.

> All subsequent packages are held in receiver's side until the lost package is retransmitted and arrives at the receiver.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.20
ㅁ References:
 - https://en.wikipedia.org/wiki/Head-of-line_blocking
 - http://www.netmanias.com/ko/post/qna/2607
 - https://hpbn.co/building-blocks-of-tcp/
```

TCP provides the abstraction of a reliable network running over an unreliable channel, which includes basic packet **error checking and correction**, **in-order delivery**, **retransmission** of lost packets, as well as flow control, congestion control, and congestion avoidance designed to operate the network at the point of greatest efficiency. Combined, these features make TCP the preferred transport for most applications.

However, while TCP is a popular choice, it is not the only, nor necessarily the best choice for every occasion. Specifically, some of the features, such as in-order and reliable packet delivery, are not always necessary and can introduce unnecessary delays and negative performance implications.

To understand why that is the case, recall that every TCP packet carries a unique sequence number when put on the wire, and the data must be passed to the receiver in-order (Figure 2-8). If one of the packets is lost en route to the receiver, then all subsequent packets must be held in the receiver’s TCP buffer until the lost packet is retransmitted and arrives at the receiver. Because this work is done within the TCP layer, our application has no visibility into the TCP retransmissions or the queued packet buffers, and must wait for the full sequence before it is able to access the data. Instead, it simply sees a delivery delay when it tries to read the data from the socket. This effect is known as `TCP head-of-line (HOL) blocking`.

The delay imposed by head-of-line blocking allows our applications to avoid having to deal with packet reordering and reassembly, which makes our application code much simpler. However, this is done at the cost of introducing unpredictable latency variation in the packet arrival times, commonly referred to as jitter, which can negatively impact the performance of the application.

Further, some applications may not even need either reliable delivery or in-order delivery: if every packet is a standalone message, then in-order delivery is strictly unnecessary, and if every message overrides all previous messages, then the requirement for reliable delivery can be removed entirely. Unfortunately, TCP does not provide such configuration — all packets are sequenced and delivered in order.

Applications that can deal with out-of-order delivery or packet loss and that are latency or jitter sensitive are likely better served with an alternate transport, such as UDP.

<img src="https://github.com/agongi/study/blob/master/tcp-head-of-line/images/Screen%20Shot%202016-08-20%20at%2019.54.48.png" width="75%">
