## P2P
Peer-To-Peer communication contrast to traditional Client-To-Server model.

>###### `STUN` is for checking IP, PORT of client running behind a NAT from incoming request. `TURN` is simply relaying all packet from A to B based on destination IP, PORT. `UDP Hole Punching` is a technique helping each peers sending or receiving packets directly even if running behind a NAT.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.11 ~ 6.30
ㅁ References:
 - http://gamedevforever.com/62
 - http://www.netmanias.com/ko/?m=view&id=blog&no=6264
 - http://www.netmanias.com/ko/?m=view&id=blog&no=6263
```

#### NAT
###### 1. STUN (Session Traversal Utilities for NAT)

 - Client requests to `STUN` server to get own public information such as IP, PORT.
 - STUN server will check the IP, PORT of incoming request (from a client running behind a NAT) and respond back to client

###### 2. TURN (Traversal Using Relays around NAT)

  - Client sends all packets to `TURN` server for relaying
  - `TURN` server will check IP, PORT of destination and relay to it

<img src="images/Screen%20Shot%202016-05-13%20at%2000.49.17.png" width="75%">

###### 3. ICE (Interactive Connectivity Establishment)
It will determine which protocol is perfect fit it this condition. (TURN or STUN or something else)

###### 4. Hole Punching (STUN)
<img src="images/Screen%20Shot%202016-05-13%20at%2000.49.43.png" width="75%">

<img src="images/Screen%20Shot%202016-05-13%20at%2000.49.52.png" width="75%">

<img src="images/Screen%20Shot%202016-05-13%20at%2000.50.09.png" width="75%">
