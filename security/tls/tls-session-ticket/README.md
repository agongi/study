## TLS Session Ticket
TLS Session Ticket is an extension to speed up the opening of TLS handshake between two endpoints.

>###### client stored `Session Ticket` after initial connection and reuse it later along with sending "Client Hello" packet. If it is valid, the server may send the same session state and continue shorten TLS-handshake

```
@author: suktae.choi
- https://timtaubert.de/blog/2014/11/the-sad-state-of-server-side-tls-session-resumption-implementations/
- https://www.ietf.org/rfc/rfc5077.txt
- http://security.stackexchange.com/questions/73777/ssl-tls-session-resumption-with-session-tickets
- http://stackoverflow.com/questions/19939247/ssl-session-tickets-vs-session-ids
```

1) First TLS-handshake request initiated sending "Client Hello" along with **empty Session Ticket**

2) The server will answer with an `New Session Ticket` extension in its "Server Hello" message if it supports it

>###### it will contain the complete session state (including the master secret negotiated between the client and the server and the cipher suite used)

>###### If one of them does not support this extension, they can fallback to the session identifier mechanism built into TLS.

3) The client should store it and present it in the “Client Hello” message of the next session

>###### this state is encrypted and integrity-protected by a key known only by the server.

4) The server finds the corresponding session in its cache and accepts to resume the session, it will send back the same session identifier and will continue with the abbreviated TLS-handshake

>###### If cache is missing, it will issue a new session identifier and switch to a full handshake

>###### This extension doesn't impact clients that do not know about them. A given TLS connection will use a ticket only if the client explicitly announces support for the extension in the 'Client Hello' message. If a client does not support TLS session tickets (e.g. IE on Windows 7), then it won't talk about session tickets in its ClientHello, and neither will the server say anything about session tickets.
