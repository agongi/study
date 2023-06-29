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

## @PersistenceContext

entityManager 는 기본적으로 transaction-scope 로 동작합니다. 즉 현재 tx 를 실행하는 thread 단위에서만 영속성이 유효합니다 (persistence context 의 동시성 이슈) 

- https://batory.tistory.com/497)
  - proxy 를 가져온다
  - 실제로 사용이되는 시점에 thread 단위에서 가져오거나 생성된 em 으로 실행한다
- https://colevelup.tistory.com/21