## WebFlux

```
ㅁ Author: suktae.choi
ㅁ References:
- https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html
```

### Overview

#### Programming Models

- Annotated Controllers
- Functional endpoints

> The big difference with annotated controllers is that the application is in charge of request handling from start to finish versus declaring intent through annotations and being called back.

```java
/* Annotated Controller *
// ..

/* Functional endpoint */
// ..
```

#### Spring MVC or WebFlux

![spring-mvc-and-webflux-venn](images/spring-mvc-and-webflux-venn.png)

If Blocking-IO (ex. filter, servlet or JPA) is imperative in your application, Spring MVC is the best suitable and apply partial reactive-components. (ex. WebClient, Reactive-Mongo ..)

#### Performance

The key expected benefit of reactive and non-blocking is the ability to scale with a ``small, fixed number of threads`` and ``less memory``.

#### Concurrency Model

What if you do need to use a blocking library? Both Reactor and RxJava provide the `publishOn` operator to continue processing on a different thread

> The schedulers have names that suggest a specific concurrency strategy — for example, “parallel” (for CPU-bound work with a limited number of threads) or “elastic” (for I/O-bound work with a large number of threads)

### Reactive Core

- HttpHandler - Basic req/res handling model
- WebHandler - higher abstraction of `HttpHandler`

