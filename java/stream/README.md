## Java 8
TDB

> ddd

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.11.12
ㅁ Origin:
 - Functional Programming in Java 8
ㅁ References:
 - http://d2.naver.com/helloworld/4911107
 -
```

### terms
모든 Collection은 stream()을 가진다.

- map() : 변경
 - input에 대해 모두에게 동일하게 적용되는 처리를 하고, output을 return
 - size는 input collection과 같다

 > ex.) name -> name.toUpperCase();

- filter() : 선택
 - input에 대해 특정을 조건으로 필터링하고, output을 return
 - size는 input collection보다 작거나, 같다

 > ex.) name -> name.length > 3

- reduce() : 변환
 - input에 대해 하나의 값을 설정하고, ouput을 return
 - size는 1 이다

 > ex.) (name1, name2) -> (name1.length >= name2.length) ? name1 : name2

- collect() : subset of reduce
 -
