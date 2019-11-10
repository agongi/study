### Read Concern

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.mongodb.com/manual/reference/read-concern/
```

<img src="images/download.png" width="75%">

### Prerequisites

#### Read Preferences

##### Primary

Read from primary.

If primary is unavailable, then failed

> Multi-Document transaction only works on this mode.

##### PrimaryPreferred

Read from primary.

If primary is unavailable, then read from secondary

##### Secondary

Read from secondary.

If secondary is unavailable, then failed

##### SecondaryPreferred

Read from secondary.

If secondary is unavailable, then read from primary

##### Nearest

Read from the lowest network latency node.

Choose candidates that has less latency than maxStalenessSeconds randomly

### Overview

#### local



#### available

orphan document 에 대한 정리필요

#### majority

PSA (Primary-Secondary-Arbiter) 모델사용시, majority 를 사용하면안됨

> Arbiter 는 투표만 할뿐이므로, PS 중 1대가 응답지연시 majority 를 항상 달성하지 못함

#### linearizable

#### snapshot