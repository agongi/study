### Read Concern

```
@author: suktae.choi
- https://docs.mongodb.com/manual/reference/read-concern/
```

<img src="images/download.png" width="50%">

#### Index

- [local](local)
- [available](available)
- [majority](majority)
- linearizable
- snapshot

### Read Preferences

#### Primary

Read from primary.

If primary is unavailable, then failed

> Multi-Document transaction only works on this mode.

#### PrimaryPreferred

Read from primary.

If primary is unavailable, then read from secondary

#### Secondary

Read from secondary.

If secondary is unavailable, then failed

#### SecondaryPreferred

Read from secondary.

If secondary is unavailable, then read from primary

#### Nearest

Read from the lowest network latency node.

Choose candidates that has less latency than maxStalenessSeconds randomly.

> Useful in multi-region circumstance.