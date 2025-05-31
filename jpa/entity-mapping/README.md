# 엔티티 매핑
```
https://www.nowwatersblog.com/jpa/ch10
```

## 기본키 매핑
- 직접생성
  - @Id 컬럼을 Entity 를 만들때 직접 설정하는 방식
- IDENTITY
  - DB 에 위임 (auto_increment)
  - `@GeneratedValue (strategy = GenerationType.IDENTITY)`
- SEQUENCE
  - 생성할 시퀀스를 지정 
  - `@GeneratedValue (strategy = GenerationType.SEQUENCE, generator ="BOARD_SEQ_GENERATOR")`
- TABLE
  - 키 생성 전용 테이블 사용
- AUTO
  - dialect 에 따라 3가지 방식중 선택
  - `@GeneratedValue (strategy = GenerationType.AUTO)`

```
JPA는 성능 향상을 위해 트랜잭션 커밋 시점에 SQL을 일괄 실행하지만,
IDENTITY/SEQUENCE/TABLE 전략은 DB에서 PK를 생성하므로 persist() 시점에 즉시 INSERT 쿼리가 실행됩니다.

(Persisten Context 에 엔티티를 관리하려면 @Id 값을 Entity 식별자로 사용 해야 하므로 -> Dirty Check 를 위한 동등성 비교)
```

## 컬럼 매핑
- @Column
  - 모든 컬럼에 정의
- @Enumerated
  - ENUM 저장 방식 (STRING/ORDINAL)

```java
@Enumerated (EnumType.STRING)
private Rolerype rolerype;

// 아래와 같이 사용 
member.setRolerype(RoleType.ADMIN); // DB에 문자 ADMIN으로 저장됨
```

- @Temporal
  - Date, Time, DateTime, LocalDate, LocalDateTime, Instant 등에 지정
  - AtttributeConverter 를 상속한 `Jsr310JpaConverters` 가 기본 제공되어서 이제 따로 정의 하지 않아도됨

```java
public class Jsr310JpaConverters {
    @Converter(autoApply = true)
    public static class LocalDateConverter implements AttributeConverter<LocalDate, Date> {
        @Nullable
        @Override
        public Date convertToDatabaseColumn(LocalDate date) {
            return date == null ? null : LocalDateToDateConverter.INSTANCE.convert(date);
        }

        @Nullable
        @Override
        public LocalDate convertToEntityAttribute(Date date) {
            return date == null ? null : DateToLocalDateConverter.INSTANCE.convert(date);
        }
    }
    
    // ...
}
```

- @Lob
  - Text, BLOB 에 지정
- @Transient
  - JPA 에서 저장/조회 하지 않을 필드
- @Access(AccessType.FIELD/PROPERTY)
  - 필드 접근시 직접접근 or Getter/Setter 지정

```java
// 수정 일시
@LastModifiedDate
@Column(name = "MOD_YMDT")
@AccessType(AccessType.Type.PROPERTY)
private Instant modDate;

// PROPERTY 방식으로 지정해서 Setter 를 통한 기본값 세팅
public void setModDate(Instant date) {
    if (Objects.isNull(date)) {
        modDate = Instant.now();
        return;
    }

    modDate = date;
}

// Getter ..
```

## 연관관계
### @OneToMany/@ManyToOne
- @OneToMany(mappedBy = B)
  - 연관관계 대상
  - mappedBy 으로 연관관계 주인 필드명 지정
- @ManyToOne; @JoinColumn
  - 연관관계 주인 (F.K 을 정의한 쪽이 주인 입니다)
  - @JoinColumn 으로 F.K 지정

```java
/**
 * User Entity
 */
@OneToMany(fetch = FetchType.LAZY, mappedBy = "user")
private Set<Order> orders = new LinkedHashSet<>();

/**
 * Order Entity
 */
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "USER_ID")
private User user;
```

### @OneToOne
- 주 테이블에 F.K 정의
  - proxy 를 통한 lazy-load 가 가능합니다 (F.K is not null 이면 대상이 존재함이 보장되므로 proxy 사용가능 즉 eager 불필요)
    - proxy 가 아직 row 를 조회하진 않았지만 존재유무는 알아야 하므로 (proxy 와 null 은 다르다) 존재유무에 대한 보장이 필요
- 대상 테이블에 F.K 정의
  - eager-load 만 가능합니다

```java
/**
 * User Entity
 */
@OneToOne(mappedBy = "user")
private Order order = new LinkedHashSet<>();

/**
 * Order Entity
 */
@OneToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "USER_ID")
private User user;
```

### @ManyToMany
- @JoinTable
  - 연관관계 주인이 @JoinTable 을 명시합니다 (@JoinColumn 과 동일함)
  - 연관관계 대상은 mappedBy 를 명시합니다

```java
@ManyToMany(fetch = FetchType.EAGER)
@JoinTable(name = "TABLE_NAME", joinColumns = @JoinColumn(name = "PERSON_ID"), inverseJoinColumns = @JoinColumn(name = "PRODUCT_ID"))
@Where(clause = "DEL_YN <> 1")   // 유효한 상품만 조회
@Fetch(FetchMode.SUBSELECT)
private Set<Order> orders = new LinkedHashSet<>();

@ManyToMany(mappedBy = "orders")
private Set<User> users = new LinkedHashSet<>();
```

@JoinTable 방식은 `joinColumns/inverseJoinColumns` 을 통해 2개의 컬럼만 사용가능해서 테이블 확장이 불가능합니다.

그래서 별도의 매핑테이블을 만들고 (대상1) OneToMany -- ManyToOne (매핑테이블) ManyToOne -- OneToMany (대상2) 로 연결하는게 확장성이 있습니다

<img src="1.png" width="50%">