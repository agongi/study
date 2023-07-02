# Spring Data JPA

```
@author: suktae.choi
```

## CUD

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

## SELECT

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT m.id FROM Member m WHERE m.id = :id")
@QueryHints({
    @QueryHint(name = "javax.persistence.lock.timeout", value = "5000"), // lock timeout 설정 (5초)
    @QueryHint(name = QueryHints.COMMENT, value = "full(member)") // sql hint 설정
})
@Transactional
public Long findById(Long id);
```

### @Lock

- (낙관적락) **NONE**
  - (@Version 사용시 기본적으로 적용되므로 명시하지 않아도 됨) 엔티티 수정시 버전을 체크합니다
  - (동작) 트랜잭션 커밋시 버전정보를 SELECT 해서 영속성의 버전과 일치함을 확인
  - (목적) 수정시 다른 트랜잭션에 의해 변경되지 않음을 보장
- (낙관적락) OPTIMISTIC
  - 엔티티를 조회할때도 버전을 체크합니다
  - (목적) repeatable-read 수준의 격리 보장
- (낙관적락) OPTIMISTIC_FORCE_INCREMENT
  - 엔티티를 수정하지 않아도 버전을 증가합니다
  - (목적) aggregate-root 에서 관리하는 연관관계의 수정시 논리적으로 같이 root 까지 업데이트 대상에 포함
- (비관적락) PESSIMISTIC_READ
  - (동작) select .. for share
- (비관적락) **PESSIMISTIC_WRITE**
  - (동작) select .. for update
- (비관적락) PESSIMISTIC_FORCE_INCREMENT
  - (동작) select .. for update + version++

### QueryHint

@Lock 사용시 timeout 을 지정해야 합니다. (select .. for share/update `nowait` 처럼 nowait 쿼리가 아니므로 무한히 대기함)

- @QueryHint(name = "javax.persistence.lock.timeout", value = "5000")
- em.setHint("javax.persistence.query.timeout", 5000)