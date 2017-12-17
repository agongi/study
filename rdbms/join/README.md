## Join

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.20
ㅁ References:
- http://rapapa.net/?p=311
- http://blog.naver.com/PostView.nhn?blogId=ssayagain&logNo=90036001354
- http://www.jidum.com/jidums/view.do?jidumId=167
- http://wiki.gurubee.net/pages/viewpage.action?pageId=26744652
```

<img src="images/Screen%20Shot%202017-07-23%20at%2012.38.04.png" width="75%">

### Join Types
#### Inner Join (== Join)
**Intersection** of both tables

```sql
-- Explicit Inner Join
mysql> select name, phone, selling
from demo_people join demo_property
on demo_people.pid = demo_property.pid;

+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm
| Mr Pullen | 01380 724040 | The Willows
| Mr Pullen | 01380 724040 | Tall Trees
| Mr Pullen | 01380 724040 | The Melksham Florist
+———–+————–+———————-+

-- Implicit Inner Join
mysql> select name, phone, selling
from demo_people, demo_property
where demo_people.pid = demo_property.pid;
```

#### Left Outer Join (== Left Join)
All **left table's row must present** and fill-out with right table's column

```sql
mysql> select name, phone, selling
from demo_people left join demo_property
on demo_people.pid = demo_property.pid;

+————+————–+———————-+
| name | phone | selling |
+————+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm
| Miss Smith | 01225 899360 | NULL
| Mr Pullen | 01380 724040 | The Willows
| Mr Pullen | 01380 724040 | Tall Trees
| Mr Pullen | 01380 724040 | The Melksham Florist
+————+————–+———————-+
```

#### Right Outer Join (== Right Join)
All **right table's row must present** and fill-out with left table's column

```sql
mysql> select name, phone, selling
from demo_people right join demo_property
on demo_people.pid = demo_property.pid;

+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm
| Mr Pullen | 01380 724040 | The Willows
| Mr Pullen | 01380 724040 | Tall Trees
| Mr Pullen | 01380 724040 | The Melksham Florist
| NULL | NULL | Dun Roamin
+———–+————–+———————-+
```

#### Outer Join (== Left + Right join)
**Combination** of both right and left join

```sql
mysql> select name, phone, selling
from demo_people outer join demo_property
on demo_people.pid = demo_property.pid;

+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm
| Miss Smith | 01225 899360 | NULL
| Mr Pullen | 01380 724040 | The Willows
| Mr Pullen | 01380 724040 | Tall Trees
| Mr Pullen | 01380 724040 | The Melksham Florist
| NULL | NULL | Dun Roamin
+———–+————–+———————-+
```

#### Cross Join
**Multiply** table A and B. The result set is N * M

**Join key** clauses are **not specified** in cross join

<img src="images/Screen%20Shot%202017-12-18%20at%2002.11.14.png" width="75%">

```sql
-- Explicit Inner Join
mysql> select name, phone, selling
from demo_people cross join demo_property

+———–+————–+———————-+
| name | phone | selling |
+———–+————–+———————-+
| Mr Brown | 01225 708225 | Old House Farm
| Mr Brown | 01225 708225 | The Willows
| Mr Brown | 01225 708225 | Tall Trees
| Mr Brown | 01225 708225 | The Melksham Florist
| Mr Brown | 01225 708225 | Dun Roamin
| Miss Smith | 01225 899360 | Old House Farm
| Miss Smith | 01225 899360 | The Willows
| Miss Smith | 01225 899360 | Tall Trees
| Miss Smith | 01225 899360 | The Melksham Florist
| Miss Smith | 01225 899360 | Dun Roamin
| Mr Pullen | 01380 724040 | Old House Farm
| Mr Pullen | 01380 724040 | The Willows
| Mr Pullen | 01380 724040 | Tall Trees
| Mr Pullen | 01380 724040 | The Melksham Florist
| Mr Pullen | 01380 724040 | Dun Roamin
+———–+————–+———————-+

-- Implicit Inner Join
mysql> select name, phone, selling
from demo_people, demo_property
```

### [Join Methods](http://blog.naver.com/PostView.nhn?blogId=ssayagain&logNo=90036001354)
#### Nested Loops
<img src="images/Screen%20Shot%202017-09-19%20at%2002.02.14.png" width="75%">

- 선행 테이블을 기준으로, 후행 테이블을 랜덤 액세스 하며 조인
  - 선행(Driving) 테이블의 크기가 작거나, Where 절 통해 결과 집합을 작게해야함
  - 후행(Driven) 테이블을 랜덤 액세스, 결과 집합이 많으면 수행속도가 저하됨

> 실행속도 == 선행 테이블 사이즈 * 후행 테이블 접근횟수

```sql
select /*+ use_nl(b,a) */ a.dname, b.ename, b.sal
from emp b, dept a
where a.loc = 'NEW YORK'
and b.deptno = a.deptno
```

#### Sort Merge
<img src="images/Screen%20Shot%202017-09-19%20at%2002.11.14.png" width="75%">

- 테이블을 조인키에 따라 정렬하고, 순차검색 하면서 같은 값 머지
  - 결과집합의 크기가 차이가 많이 나는 경우에는 비효율적
  - 조인 연결고리의 비교 연산자가 범위 연산인 경우 Nested Loop 조인보다 유리

> 실행속도 == 정렬 cost

```sql
select /*+ use_merge(a b) */ a.dname, b.empno, b.ename
from   dept a,emp b
where  a.deptno = b.deptno
and    b.sal > 1000 ;
```

#### Hash
- 선행 테이블의 조인키를 Hash 해서 Bucket 생성
- 수행 테이블의 조인키도 Hash 하며, 같은 해쉬값을 가진 키 중에서 실제로 같으면 조인 (충돌 있으므로)

```sql
select /*+ use_hash(a b) */ a.dname, b.empno, b.ename
from dept a, emp b
where a.deptno = b.deptno
and a.deptno between 10 and 20;
```

### Union vs Join
```sql
Table1 (1, 2, 3, 4)
Table2 (3, 4, 5, 6)
```

- Union
```sql
Table1 UNION Table2

id
---
1
2
3
4
3
4
5
6
```

- Outer Join
```sql
Table1 t1 OUTER JOIN Table2 t2 ON t1.id = t2.id

id    id
---------
1    NULL
2    NULL
3     3
4     4
NULL  5
NULL  6
```

- Inner Join
```sql
Table1 t1 INNER JOIN Table2 t2 ON t1.id = t2.id

id    id
---------
3     3
4     4
```
