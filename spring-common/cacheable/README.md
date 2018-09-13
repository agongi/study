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
<cache:annotation-driven cache-manager="compositeCacheManager" proxy-target-class="true" mode="proxy"/>

<bean id="compositeCacheManager" class="org.springframework.cache.support.CompositeCacheManager">
    <property name="cacheManagers">
        <list>
            <ref bean="ehCacheManager" />
            <ref bean="springRedisCacheManager" />
        </list>
    </property>
</bean>
```

### How To Use
```java
@Cacheable(value = "cacheName", key = "#key", unless= "#result == null")
public void cacheableMethod(int key, int intValue, String stringValue) {
  // ...
}

@CacheEvict(value = "cacheName", key = "#key")
public void cacheEvictMethod(String battleKey) {
  // ...
}
```

- Precondition
  - The same parameters always guarantee the same return
  - The result is stored in cache defined in configuration like ehCache or redis
  - If result is null, it is not stored in cache

If the same key comes as parameter defined in key attribute of @Cacheable (e.g. key = "#key"), method body will not be executed and get result from cache
