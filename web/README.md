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

## [CORS (Cross-Origin Resource Sharing)](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F)
<img src="1.png" width="50%">

Same Origin 은 `프로토로://도메인:포트` 가 동일함을 의미합니다. (동일하지 않다면 Cross Origin)

`브라우져는 기본적으로 Same Origin 에 대한 리소스 호출만을 허용`합니다.
- img, css 는 Cross Origin 을 허용하고 (ex. CDN 도메인)
- script (ex. Fetch/XMLHttpRequest 등 js 에서 API 호출) 는 Same Origin 만 허용합니다

해당 제약을 허용할 방식이 CORS 이고 아래와 같이 동작합니다:
- (CLIENT) 요청 > `Origin`
- (SERVER) 응답 > `Access-Control-Allow-Origin`
- (CLIENT) 브라우저는 `Origin == Access-Control-Allow-Origin` 비교후 일치하면 성공처리

> 판단의 주체가 `브라우저` 입니다

CORS 를 위해서 전체적인 동작을 설명하면:
- 예비호출
  - (preflight) OPTIONS 로 호출해서 `Access-Control-Allow-*` 헤더값 확인
- 본호출
  - 실제 API 호출
- (이후) Access-Control-Max-Age 에 설정된 시간만큼 예비호출 스킵
  - 서버에서 Max-Age 를 설정하지 않았다면 매 호출마다 preflight 수행

<img src="2.png" width="50%">

해당 동작을 처리하는 nginx 의 설정은 아래와 같습니다:
```
# preflight
if ($request_method = 'OPTIONS') {
    # allowed origin
    add_header Access-Control-Allow-Origin '*';

    # allowed header
    add_header 'Access-Control-Allow-Headers' '*';

    # allowed method
    add_header 'Access-Control-Allow-Methods' 'GET';

    # preflight request cache-ttl
    add_header 'Access-Control-Max-Age' '86400';

    return 204;
}

# request
add_header Access-Control-Allow-Origin '*' always;
```

## [Post Message](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage)

## [XSS (Cross-Site Scripting)](https://dj-min43.medium.com/xss-%EA%B3%B5%EA%B2%A9%EC%9D%84-%EC%A7%81%EC%A0%91-%ED%95%B4%EB%B3%B4%EB%A9%B4%EC%84%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0-c2c1d9baf7ec)
공격자가 화면에 실행할 수 있는 \<script> 를 삽입/실행할 수 있는 취약점 입니다.
- form
- input

<img src="3.png" width="50%">

```html
<div>
<span>검색값: c/span>
    <span id="search-input-result">
    <script>alert("script 공격");</script>
</span>
</div>
```

아래의 조치로 방어가 필요합니다:
- 입력값의 Escape
  - \<script> -> &lt;script&gt;
- 리액트는 JSX 를 렌더링 할때 escape 처리 합니다

<img src="4.png" width="50%">

## [CSP (Content-Security-Policy)](https://brunch.co.kr/@sangjinkang/43)
스크립트를 실행할 수 있는 Cross Origin 을 지정하는 방식으로, 위에 XSS 방식보다 좀 더 강력한 접근 방식입니다
- XSS 는 실행은 되지만 escape 처리
- CSP 는 실행 자체를 막는 방법

```html
<!-- 서버의 응답헤더 -->
Content-Security-Policy: frame-ancestors 'none';
Content-Security-Policy: default-src 'self' *.naver.com

<!-- 브라우저는 응답헤더에 있는 Origin 을 보고 일치유무 확인 -->
```

Cross Origin 에서 \<iframe> 을 사용시 (ex. \<iframe src="https://youtube.com"/> CSP 헤더를 통해 방지할 수 있습니다

## [CSRF (Cross Site Request Forgery)](https://dev-ino.tistory.com/35)
Cross Origin 에서 의도하지 않은 요청을 실행하는 취약점 입니다
- Cross Site 를 만들고 해당 페이지로 유도
- 유저는 현재 로그인된 자신의 쿠키를 가지고 있다면

```html
<!-- GET 요청이 즉시 실행되도록 img,css 를 이용해서 진입시점에 API 호출 -->
<img src="도메인/v2/delete-user">

<!-- FORM 을 만들어서 버튼 클릭시 변경 요청이 실행 -->
<form action="https://vulnerable-website.com/password/change" method="POST">
            <input type="hidden" name="password" value="mypassword">
     </form>
<script>
  document.forms[0].submit();
</script>
```

의 `가상 페이지에서 의도되지 않은 요청`을 하게 됩니다. 아래의 조치로 어느정도 방어 할 수 있습니다: 
- `Referrer 체크`
  - 최소한의 안전장치
- SameSite 쿠키 사용
  - Site 가 다른경우 쿠키가 전달됨을 막아서 에러처리 하는 방안

## Cookies
- secure
  - https 만 접근가능
- httpOnly
  - javascript 에서 접근불가능
- hostOnly
  -  window.location.host 가 cookie:domain 과 일치해야함 
- session
  - 세션 쿠키는 브라우저가 닫히거나 세션이 종료될 때까지 유효합니다.
- SameSite