# JPQL

```
@author: suktae.choi
```

// ANSI SQL
em.createNativeQuery("....")

// JPQL or HQL
em.createQuery("...", Member.class).getResultList() or .getSingleResult()

// Named
em.createNamedQuery("....")

## TypeQuery vs Query
- TypeQuery: clz.class 를 지정
- Query: Object.class 로 캐스팅

## Parameter Binding
```sql
em.createQuery("SELECT m FROM Member m where m.username = :username", Member.class)
    .setParameter("username", "xxxName")
    .getResultList();
```