## How to iterate Map

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.02.20
ㅁ References:
 - http://javarevisited.blogspot.kr/2011/12/how-to-traverse-or-loop-hashmap-in-java.html
```

### Map.keySet()
```java
for (String key : map.keySet()) {
  log.debug("key: " + key + ", value: " + map.get(key));
}
```

### Map.entrySet()
```java
for (Map.Entry<String, String> entry : map.entrySet()) {
  log.debug("key: " + entry.getKey() + ", value: " + entry.getValue());
}
```
