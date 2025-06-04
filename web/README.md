# Web
```
https://developer.mozilla.org
https://ko.javascript.info/
```

### Index
- [javascript](javascript)
- [protocols](protocols)
- [security](security)
- [http](http)
- [Tomcat](tomcat)

***
## RESTful API
URL에 자원(Resource)을 명시하고, HTTP Method에 행위(Action)를 명시하는 API

### [Method 특징](https://velog.io/@gidskql6671/HTTP-Method%EC%9D%98-%EB%A9%B1%EB%93%B1%EC%84%B1)
HTTP Method 에 대한 멱등성은 동일한 요청을 여러번 보내도 서버 상태가 동일할때를 의미합니다

- `안정성`: 여러번 호출해도 변경되지 않는 특징
  - GET
- `멱등성`: 여러번 호출해도 결과가 동일한 특징
  - GET
  - DELETE
  - PUT
  - `POST(신규생성)/PATCH(특정필드 변경 => ON/OFF 등) 은 구현에 따라 멱등성이 유지 되지 않음`
- 캐시: E-Tag or Last-Modified 등을 이용해 응답이 캐싱되는 특징
  - GET

## CORS

## XSS

## CSRF

## HTTP/2.0

## Cookies
- secure
  - https 만 접근가능
- httpOnly
  - javascript 에서 접근불가능
- hostOnly
  -  window.location.host 가 cookie:domain 과 일치해야함 
- session
  - 세션 쿠키는 브라우저가 닫히거나 세션이 종료될 때까지 유효합니다.