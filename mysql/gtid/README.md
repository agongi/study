# GTID

```
@author: suktae.choi
- https://dev.mysql.com/doc/refman/8.0/en/replication-options-gtids.html#option_mysqld_gtid-mode
```

MySQL Global Transaction ID (GTID) is a unique identifier for each transaction that occurs on a MySQL server. GTID is a feature of MySQL's replication protocol that allows you to identify and track changes made to a database on the master server and replicate them on one or more slave servers.

- GTID consists of two parts: the unique server identifier and the transaction ID. The server identifier is a UUID that identifies the MySQL server instance, and the transaction ID is a sequence number that is incremented for each transaction that occurs on the server.
- When a transaction is executed on the master server, the GTID for that transaction is recorded in the transaction's metadata. This metadata is included in the binary log, which is used to replicate changes from the master to the slave servers.
- When a slave server connects to the master server, it requests the next transaction in the binary log that it needs to replicate. The master server sends the binary log along with the GTID for each transaction.
- The slave server records the GTID of the last transaction that it replicated from the master server. This allows the slave server to resume replication from where it left off if replication is interrupted.
- When a slave server replicates a transaction from the master server, it updates its own GTID set to include the GTID of the replicated transaction. This allows the slave server to track which transactions it has already replicated and which transactions it still needs to replicate.

By using GTID, MySQL replication is more reliable and resilient to failures. It allows you to identify and track changes made to the database more easily, making it simpler to diagnose and fix replication issues.