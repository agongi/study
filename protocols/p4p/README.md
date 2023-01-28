## P4P (Proactive network provider participation for P2P)
Internet service providers(ISPs) and peer-to-peer(P2P) software to `optimize peer-to-peer connections`. P4P proponents say that it can save an ISP significant costs, and that using local connections also speeds up download times for P2P downloaders by 45%, critics say that this will favor downloaders on some ISPs but come at the expense of others.

>###### P2P tries to increase network performance based on **nearby peer location** to get over random selection mechanism in P2P.

```
ㅁ Author: suktae.choi
- https://en.wikipedia.org/wiki/Proactive_network_provider_participation_for_P2P
- http://www.netmanias.com/ko/?m=view&id=techdocs&no=5201
```

#### Background
Traditional P2P has selected peer-list randomly, It could cause serious network latency.

> Because It doesn't care of physical distance from each peers.

Some people thought tracker has no way to know `ISP information` of each peers and make a decision to establish `the server that handles network information of peer` in the same ISP region to support communication for tracker. Tracker now can get `network information of each peers` and refer to it before generating peer-list of specific torrent file.

> It helps tracker generates nearby peer-list.

#### 1. Client to iTracker

<img src="images/Screen%20Shot%202016-06-10%20at%2000.19.58.png" width="75%">

 - peer A downloads .torrent file from tracker
 - user runs .torrent file in BitTorrent client. Client sends IP to prefixed iTracker server
 - iTracker server is responsible for managing network information (ASID, PID, LOC) to responds back to BitTorrent client
 - client requests to iTracker with Hash of .torrent and given network information (ASID, PID, LOC)
 - iTracker is responsible for making peer-list based on networking information which matches or closer and responds it
 - client starts downloading file (p2p handshake and connection)

#### 2. pTracker to iTracker

<img src="images/Screen%20Shot%202016-06-10%20at%2000.20.05.png" width="75%">

 - peer A downloads .torrent file from tracker
 - client requests to iTracker with Hash of .torrent and client IP
 - pTracker sends IP of client to iTracker to get network information based on client's IP
 - iTracker server is responsible for managing network information (ASID, PID, LOC) to responds back to pTracker
 - iTracker is responsible for making peer-list based on networking information which matches or closer and responds it
 - client starts downloading file (p2p handshake and connection)

###### Comparision

<img src="images/Screen%20Shot%202016-06-10%20at%2000.06.25.png" width="75%">
