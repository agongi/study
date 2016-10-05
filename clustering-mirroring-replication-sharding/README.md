## Clustering vs Mirroring vs Replication vs Sharding
TBD

>###### TBD

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.09
ㅁ References:
 - http://bcho.tistory.com/670
 - http://bcho.tistory.com/672
 - http://bcho.tistory.com/673
 - http://bcho.tistory.com/674
 - https://social.msdn.microsoft.com/Forums/sqlserver/en-US/d943a6a8-234e-4534-8691-db90c4937c83/clustering-vs-replication?forum=sqlreplication
```

<img src="https://github.com/agongi/study/master/clustering-mirroring-replication-sharding/images/Capture.PNG" width="75%">

1. Clustering (Fail-Over)
Two physical machines can share some resources. At any one time one physical node will host the DB Server. If the physical node fails, or if the operator initiates a failover, the SQL Server will go offline and restart on the other physical node. But the SQL Server is only hosted on one node at any one time.<br><br>
The clients will connect to this SQL Server by its virtual name and will be redirected to the physical host which runs the SQL Server on failover automatically.

2. Mirroring (@Sync/Copy-To-Slave)
The same as replication without synchronize mechanism. It is sync.

3. Replication (@Async/Copy-To-Slave)
Replication copies data from a source server to a destination server and keeps both copies the same but there may be some latency.
It can copy the entire database or a part of it.

4. Sharding (Horizontal-Partitioning)
For performance,
