## TLS ALPN Extension
NPN and ALPN are TLS extensions to negotiate Application-Layer protocol in TLS handshaking time to reduce additional round-trip.

> **Server responds** supported protocol list to client in NPN, **client requests** with the list to server in ALPN.

```
ㅁ Author: suktae.choi
- https://tools.ietf.org/html/rfc7301
- https://tools.ietf.org/id/draft-agl-tls-nextprotoneg-03.html
```

### NPN (Next Protocol Negotiation)
#### How it works
- [ClientHello] client -> server
 - client sets TLS extension NPN
- [ServerHello] server -> client
 - server responds supported Application-Layer protocol list
- Client chooses the one from given list

> For security issues, NPN is prohibited in HTTP/2 spec due to server sending supported Application-Layer protocol list to client in plain/text before TLS handshaking is done.

### ALPN (Application-Layer Protocol Negotiation)
#### How it works
- [ClientHello] client -> server
 - client sets TLS extension ALPN with supported application protocol list
- [ServerHello] server -> client
 - Server picks up the one and responds

> ALPN is the next protocol negotiation strategy in HTTP/2
