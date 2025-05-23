# Invariant vs Covariant
```
https://jackjeong.tistory.com/54?category=802500
```

제네릭은 `불공변을 기본으로 타입 안정성을 보장`합니다.

## Covariant (공변)
Array 는 covariant (공변) 이다

```java
String[] strings = new String[] { "A", "B" };
Object[] objects = strings; // 컴파일 가능 (공변)

objects[0] = 100; // ❌ 런타임 오류 (공변 - ArrayStoreException)
```

- 컴파일타임: 가능
- 런타임
  - 잘못된 타입의 CUD 발생시 ArrayStoreException 이 발생한다.


## Invariant (불공변)
Collection 은 invariant (불공변) 이다

```java
List<String> strings = new ArrayList<>();
List<Object> objects = strings; // ❌ 컴파일 오류 (불공변)
```

- 컴파일타임: 불가능
- 런타임
  - 성립하지 않음