## @Cacheable

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.01
ㅁ References:
 - https://blog.outsider.ne.kr/1094
 - http://dev.anyframejava.org/docs/anyframe/plugin/optional/cache/1.0.3/reference/html/ch01.html
 - https://docs.spring.io/spring/docs/current/spring-framework-reference/html/cache.html#cache-annotations
```

### Configure
```xml
compositeCacheManager

```

### How To Use
```java
@Cacheable(value = "#key")
public void xxx(int key, String b) {

}

```

- The same method parameters always guarantee the same return
  - If the same input comes, immediately read result in cache
- Cache key might be defined in annotation
