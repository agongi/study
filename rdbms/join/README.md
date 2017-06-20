## Join

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.20
ㅁ References:
 - http://rapapa.net/?p=311
```

<img src="https://github.com/agongi/study/blob/master/rdbms/join/images/Visual_SQL_JOINS_V2.png" width="75%">


### Tables
```sql
mysql> select * from demo_people;

+————+————–+——+
| name | phone | pid |
+————+————–+——+
| Mr Brown | 01225 708225 | 1 |
| Miss Smith | 01225 899360 | 2 |
| Mr Pullen | 01380 724040 | 3 |
+————+————–+——+

mysql> select * from demo_property;

+——+——+————————+
| pid | spid | selling |
+——+——+————————+
| 1 | 1 | Old House Farm |
| 3 | 2 | The Willows |
| 3 | 3 | Tall Trees |
| 3 | 4 | The Melksham Florist |
| 4 | 5 | Dun Roamin |
+——+——+————————+
```
#### Inner Join (== Join)
**Intersection** of both tables

```sql
mysql> select name, phone, selling
from demo_people join demo_property
on demo_people.pid = demo_property.pid;

+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm |
| Mr Pullen | 01380 724040 | The Willows |
| Mr Pullen | 01380 724040 | Tall Trees |
| Mr Pullen | 01380 724040 | The Melksham Florist |
+———–+————–+———————-+
```

#### Left Join
All left table's row must present and fill-out with right table's column

```sql
mysql> select name, phone, selling
from demo_people left join demo_property
on demo_people.pid = demo_property.pid;

+————+————–+———————-+
| name | phone | selling |
+————+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm |
| Miss Smith | 01225 899360 | NULL |
| Mr Pullen | 01380 724040 | The Willows |
| Mr Pullen | 01380 724040 | Tall Trees |
| Mr Pullen | 01380 724040 | The Melksham Florist |
+————+————–+———————-+
```

#### Right Join
All right table's row must present and fill-out with left table's column

```sql
mysql> select name, phone, selling
from demo_people right join demo_property
on demo_people.pid = demo_property.pid;

+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm |
| Mr Pullen | 01380 724040 | The Willows |
| Mr Pullen | 01380 724040 | Tall Trees |
| Mr Pullen | 01380 724040 | The Melksham Florist |
| NULL | NULL | Dun Roamin |
+———–+————–+———————-+
```

#### Outer Join
**Union** of both tables

```sql
+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm |
| Miss Smith | 01225 899360 | NULL |
| Mr Pullen | 01380 724040 | The Willows |
| Mr Pullen | 01380 724040 | Tall Trees |
| Mr Pullen | 01380 724040 | The Melksham Florist |
| NULL | NULL | Dun Roamin |
+———–+————–+———————-+
```
