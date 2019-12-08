### Transactions

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.mongodb.com/manual/core/write-operations-atomicity/
```

### Atomicity

All write operations in MongoDB are atomic on the level of a single document.

#### Multi-Document

Multi-document transactions are atomic (i.e. `all-or-nothing` proposition)

- **In version 4.0**, MongoDB supports multi-document transactions on replica sets.
- **In version 4.2**, MongoDB introduces distributed transactions, which adds support for multi-document transactions on replica sets and `sharded clusters`.

> In most cases, multi-document transaction incurs a greater performance cost over single document writes.



