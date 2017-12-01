## HTTP Cache-Control

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.23
ㅁ References:
 - http://cyberx.tistory.com/9
 - https://www.netmanias.com/ko/post/blog/5654/cdn-http/http-cache-control-expiration-and-validation
 - https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching
 - https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching
```

### Request
#### Validation
Cache is or about to be expired, It is needed to be verified

- Last-Modified-Since (HTTP/1.0)
  - Date
- If-None-Match (HTTP/1.1)
  - Etag (Hash)

----->
- If-None-Match (or Last-Modified-Since)

<-----
- 200 OK, If modified
  - Replace Cache
- 304 Not Modified, If not
  - Refresh max-age

### Response
#### Freshness
TTL is still alive, It will be used

- Expires
  - Date
- Cache-Control
  - max-age: seconds

------><------
- Cache is valid, It is reused except request
