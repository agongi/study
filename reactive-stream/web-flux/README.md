## WebFlux

```
ㅁ Author: suktae.choi
ㅁ References:
- https://godekdls.github.io/Reactive%20Spring/contents/
- https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html
```

#### Index

- [Server Side Event](sse)

***

WebFlux is a kind of` Publisher<T>` in reactive-stream.

- Annotated Controllers
- Functional endpoints

> The big difference with annotated controllers is that the application is in charge of request handling from start to finish versus declaring intent through annotations and being called back.

```java
/* Annotated Controller *
// ..

/* Functional endpoint */
// ..
```

### Spring MVC or WebFlux

![spring-mvc-and-webflux-venn](images/spring-mvc-and-webflux-venn.png)
