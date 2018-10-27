## Proxy Protocol
The PROXY protocol provides a convenient way to **safely transport connection information** such as a client's address across multiple layers of NAT or TCP proxies.

> **proxy-protocol header is transferred to each endpoints additionally** just after TCP connection established

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.12.14
ㅁ References:
 - http://www.haproxy.org/download/1.7/doc/proxy-protocol.txt
 - http://blog.leedoing.com/23
```

#### Approach
It is easy to perform for the sender (just send a short header once the connection is established) and to parse for the receiver (simply perform one read() on the incoming connection to fill in addresses after an accept). The protocol used to carry connection information across proxies was thus called the PROXY protocol.
