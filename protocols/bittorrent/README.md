## BitTorrent
BitTorrent is a `communications protocol of peer-to-peer file sharing` which is used to distribute data over the Internet. BitTorrent is one of the most common protocols for transferring large files, and peer-to-peer networks have been estimated to collectively account for approximately 43% to 70% of all Internet traffic (depending on location) as of February 2009.

>###### Tracker is responsible for managing peer-list of large-file, and responds to client with peer IP/PORT address. Client request to tracker for getting information of file and P2P connect to each other based on response of tracker.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.29
ㅁ References:
 - http://www.netmanias.com/ko/post/techdocs/5185/p2p-bittorrent/understanding-of-the-bittorrent-protocol
 - http://ninetin.tistory.com/27
 - https://en.wikipedia.org/wiki/BitTorrent
 - https://wiki.theory.org/BitTorrentSpecification
```

#### 1. Download .torrent file
Download torrent file from the public and/or private tracker website. It contains `Hash` and `tracker URL`. If torrent file has the same hash but different URL, then the client information is managed by different tracker and they couldn't share the file each other.

> If tracker A has client-list {a,b,c} of file X, and tracker B has a list {d,e,f}, then each peers within the same tracker only can share the file.

> Tracker could be public and/or private on the net. It only helps client search `.torrent` file not like server based file sharing architecture.

#### 2. Tracker Request
Torrent client sends http request to specific tracker url that is mentioned in .torrent file with `peerId` and `Hash`. The tracker site is responsible for getting these request from each peers and managing peer-list-table.

<img src="images/Screen%20Shot%202016-05-30%20at%2023.23.41.png" width="75%">

#### 3. Tracker Response
Tracker responds IP, PORT of 50 peers.

> If the number of peers exceeds 50, tracker choose 50 peers randomly. This value can be modified in settings.

> Random selection of 50 peers causes serious problem. It downloads file across the region even thought there is some peer nearby. This can be solved in [P4P](https://github.com/agongi/study/tree/master/p4p/) mechanism.

#### 4. P2P Connect
 - peer A tries to connect to each peers (Handshake)
 - peers share the information of the file pieces what they have
 - peer A tries to request the piece of files that peer A doesn't have
 - file is transfer to peer A
 - peer A responds to the file piece request once It gets
 - peer A broadcast the information of the file pieces to all peers (notification)
 - peer A turns into a seeder after download the file completely

<img src="images/Screen%20Shot%202016-05-30%20at%2023.24.00.png" width="75%">
