# FetchType.Lazy vs Eager

```
@author: suktae.choi
- https://doohwan-yoo.github.io/data-jpa-3/
```

## FetchType.Lazy
- **Join** to get whatever it is used
  - Cons: join is alway performed

```sql
select
  user0_.user_no as user_no1_0_0_,
  user0_.user_name as user_nam2_0_0_,
  userextra1_.user_no as user_no1_1_1_,
  userextra1_.phone_num as phone_nu2_1_1_
from
  tbl_user user0_ left outer join
  tbl_user_extra userextra1_
on
  user0_.user_no=userextra1_.user_no
where
  user0_.user_no=?
```

## FetchType.Eager
- Query is executed more if used
  - Cons: **N + 1 query** performed

```sql
-- source table
Hibernate: select user0_.user_no as user_no1_0_0_, user0_.user_name as user_nam2_0_0_ from tbl_user user0_ where user0_.user_no=?
-- target table
Hibernate: select userextra0_.user_no as user_no1_1_0_, userextra0_.phone_num as phone_nu2_1_0_ from tbl_user_extra userextra0_ where userextra0_.user_no=?
```

> Best Practice

- @OneToMapy : 기본값=지연로딩(LAZY)
- @ManyToOne : 기본값=즉시로딩(EAGER)=> Order 정보 이외에 N : 1 관계에 있는 Entity를 묵시적으로 로딩해 와서 지연이 된 사례
