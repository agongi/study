## TCP Connection Migration
Equivalent to TCP connection repair or TCP session migration

> TBD

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.06.26
ㅁ References:
 - https://lwn.net/Articles/495304/
 - https://tools.ietf.org/html/draft-snoeren-tcp-migrate-00
 - http://blog.nattyhacker.com/2013/07/transferable-tcp-connection.html
 - http://nms.lcs.mit.edu/talks/e2e-migrate.pdf
```

client sends MIGRATION_SYN to server

MIGRATION_WAIT - state machine

rejected -> new negotiation on-going
accepted -> TCP_CLOSE
