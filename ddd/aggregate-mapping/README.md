## Aggregate Mapping

```
@author: suktae.choi
- https://www.popit.kr/jpa-%ec%97%b0%ea%b4%80-%ea%b4%80%ea%b3%84-%ec%a1%b0%ed%9a%8c-%ea%b7%b8%eb%a6%ac%ea%b3%a0-msa
- https://www.popit.kr/id%EB%A1%9C-%EB%8B%A4%EB%A5%B8-%EC%95%A0%EA%B7%B8%EB%A6%AC%EA%B2%8C%EC%9E%87%EC%9D%84-%EC%B0%B8%EC%A1%B0%ED%95%98%EB%9D%BC/
```

Domain 이 달라서 (MSA 관점에서) relational 테이블이 같은 DB 에 없을 수 있다.

이럴때 기존의 relation 으로는 처리가 안되는데, 어떤식으로 매핑을 할지 알아보겠습니다.

## Monolithic

```java
@Entity
@Table(name = "user")
public class User {
  @OneToMany(mappedBy = "userNo", fetch = FetchType.LAZY, cascade = CascadeType.ALL, orphanRemoval = true)
  private Set<Store> stores = new LinkedHashSet<>();
}
```

```java
@Entity
@Table(name = "store")
public class Store {
	private Long userNo;
  private Long id;
  // ...
}
```

Entity 를 직접 바라보는데, 만약 user/store 가 다른 DB 에 저장된다면 직접 볼수가 없다.

그래서 object 가 아닌 object reference 를 참조하고, 필요할때  N+1 로 조회해서 가져오는 방식으로 변경이 필요하다.

## MSA

```java
@Entity
@Table(name = "user")
public class User {
  @OneToMany
	@JoinColumn(name = "store_no", referencedColumnName = "id")
  private Set<Long> storeIds = new LinkedHashSet<>();
}
```

```java
public static void main(String[] args) {
  User user = userRepository.findById(123L);
  // if stores needed
  Set<Store> stores = storeSearchService.searchByIds(user.getStoreIds());
}
```

