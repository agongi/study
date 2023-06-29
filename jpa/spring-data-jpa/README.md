# Spring Data JPA

```
@author: suktae.choi
```

## SELECT

```java
@Query("UPDATE ABC_MEMBER SET MBR_STAT_TP = 'USED' WHERE MBR_NO = :id", nativeQuery = true)
@Modifying(clearAutomatically = true, flushAutomatically = true)
@Transactional
public Long update(Long id);
```

- @Query 에서 `nativeQuery = true` 설정으로 native-query 사용도 가능합니다
- @Modifying
  - flushAutomatically: JPQL (or SQL) 실행전 영속성 flush 유무
  - clearAutomatically: JPQL (or SQL) 실행후 영속성 clear 유무

## CUD

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT m.id FROM Member m WHERE m.id = :id")
@QueryHints({@QueryHint(name = "javax.persistence.lock.timeout", value = "5000")}) // lock timeout 설정 (5초)
@Transactional
public Long findById(Long id);
```

- @Lock
  - (낙관적) OPTIMISTIC_READ/WRITE 
  - (비관적) PESSIMISTIC_READ/WRITE
- @QueryHint
  - em.setHint("javax.persistence.query.timeout", 50) 와 동일한 기능 입니다