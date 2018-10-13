## TLS (Transport Layer Security)
The Transport Layer Security (TLS) Handshake Protocol is responsible for the authentication and key exchange necessary to establish or resume secure sessions. When establishing a secure session, the Handshake Protocol manages the following:

 - Cipher suite negotiation
 - Authentication of the server and optionally, the client
 - Session key information exchange.

> Simply SSL is the out-dated name of TLS.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.01.20
ㅁ References:
 - https://msdn.microsoft.com/en-us/library/windows/desktop/aa380513.aspx
 - http://www.moserware.com/2009/06/first-few-milliseconds-of-https.html
 - https://en.wikipedia.org/wiki/Cipher_suite
 - http://www.ibm.com/support/knowledgecenter/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm
 - https://tools.ietf.org/html/rfc5246
 - https://tools.ietf.org/html/rfc7301
 - https://tls.ulfheim.net
```

#### Series
- [TLS Session Tickets](https://github.com/agongi/study/tree/master/tls/tls-session-ticket/)
- [TLS ALPN (Application-Layer Protocol Negotiation)](https://github.com/agongi/study/tree/master/tls/tls-alpn/)
- [TLS 1.3](#)

### TLS Handshaking
2 entirely round-trip is done in new connection establishment. But 1 round-trip is required using existing connection.

<img src="https://github.com/agongi/study/blob/master/tls/images/Screen%20Shot%202016-12-29%20at%2001.19.05.png" width="75%">

1) The client sends a **Client hello** message to the server, along with the client's `random value` and `supported cipher-suite list` with extensions

<img src="https://github.com/agongi/study/blob/master/tls/images/Screen%20Shot%202016-12-29%20at%2001.32.55.png" width="75%">

> Random value is used to generate Master Secret both server and client.

> Cipher suite is a set of authentication, encryption, message authentication code(MAC) and key exchange algorithm.

2) The server responds by sending a **Server hello** message to the client, along with the server's `random value` and `selected cipher-suite`

<img src="https://github.com/agongi/study/blob/master/tls/images/Screen%20Shot%202016-12-29%20at%2001.57.56.png" width="75%">

3) The server sends its `certificate` to the client for authentication and may request a certificate from the client. It also sends `server key exchange` to exchange premaster secret with client. The server sends the **Server hello done** message

<img src="https://github.com/agongi/study/blob/master/tls/images/Screen%20Shot%202016-12-29%20at%2001.58.15.png" width="75%">

> The value generated in server key exchange is used as public key in diffie-hellman algorithm. The result of key is marked premaster_secret to make master_secret.

4) If the server has requested a certificate from the client, the client sends `certificate`

5) The client sends `client key exchange`. After that do `certificate verify` what server sent

6) The client creates a `random Pre-Master Secret` and encrypts it with the public key from the server's certificate, sending the encrypted Pre-Master Secret to the server

7) The server receives the Pre-Master Secret. The server and client each generate the `Master Secret` and `session keys` based on the Pre-Master Secret

```code
## "master secret" is magic_string
## client and server hello random values are exchange in phase 1 and 2

master_secret = PRF(pre_master_secret,
                    "master secret",
                     ClientHello.random + ServerHello.random)
```

8) The client sends `Change cipher spec` notification to server to indicate that the client will start using the new session keys for hashing and encrypting messages. Client also sends **Client finished** message

<img src="https://github.com/agongi/study/blob/master/tls/images/Screen%20Shot%202016-12-29%20at%2001.59.48.png" width="75%">

> Change cipher spec means that connection will be going on from asymmetric to symmetric.

9) Server receives `Change cipher spec` and switches its record layer security state to symmetric encryption using the session keys. Server sends **Server finished** message to the client

10) Client and server can now exchange application data over the secured channel they have established. All messages sent from client to server and from server to client are encrypted using session key

```
## For example, this is the one of Cipher suite.

DHE-RSA-AES128-SHA
 : DHE-RSA --> asymmetric
 : AES128 --> symmetric
 : SHA --> Hash
```
