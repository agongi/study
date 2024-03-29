## Reactive Stream

```
@author: suktae.choi
- https://github.com/reactive-streams/reactive-streams-jvm
- https://tech.kakao.com/2018/05/29/reactor-programming/
```

#### Index

- [Reactor](reactor)
- [WebFlux](webflux)
- [WebClient](webclient)

#### Blog

- [Blocking to Reactive](<http://wiki.sys4u.co.kr/pages/viewpage.action?pageId=7766994#id-%EC%97%B0%EC%8A%B5%EB%AC%B8%EC%A0%9C%EB%A1%9C%EB%B0%B0%EC%9B%8C%EB%B3%B4%EB%8A%94Reactor-11.BlockingtoReactive>)
- [Proxy server with WebFlux](https://translate.googleusercontent.com/translate_c?depth=1&hl=ko&rurl=translate.google.co.kr&sl=ja&sp=nmt4&tl=en&u=https://kazuhira-r.hatenablog.com/entry/20180408/1523190124&xid=17259,15700023,15700186,15700190,15700248,15700253&usg=ALkJrhgdKV2YylUpbK6DdnJCS77pUGhknA)
- [Flux sequence](https://www.baeldung.com/flux-sequences-reactor)
- [Spring 웹 애플리케이션에서 사용하지 않는 API를 찾아보자](http://woowabros.github.io/tools/2019/02/15/controller-log.html)

***

## Why reactive?

The main purpose of Reactive Streams is to let the subscriber to `control how quickly or how slowly the publisher` produces data.

> If a publisher cannot slow down, it has to decide whether to buffer, drop, or fail.

