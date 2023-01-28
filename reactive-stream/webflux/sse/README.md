## Server Side Event

```
ㅁ Author: suktae.choi
- https://stackoverflow.com/questions/54680001/spring-webflux-flux-behavior-with-non-streaming-application-json
- https://javacan.tistory.com/entry/spring-webflux-server-sent-event-1?category=489498
```

### SerDes

#### application/json

Buffering each chunk of Flux/Mono and flush once. 

> This behavior still means async-nonblocking mechanism.

*Request*

- Accept: application/json

```bash
http get http://localhost:8080/web-client/user --stream
```

The JSON codec configured by default in Spring WebFlux will serialize to JSON and flush to the network in one go. It will buffer the `Flux<YourObject>` in memory and serialize it in one pass. This doesn't mean the operation is blocking, since the resulting `Flux<Databuffer>` is written in a reactive fashion to the network. nothing is blocking here.

#### application/stream+json

Emits each element of Flux/Mono using SSE (Server Sent Events).

*Request*

- Accept: application/stream-json

```bash
http get http://localhost:8080/web-client/user --event-stream
```

The JSON codec configured by default in Spring WebFlux will serialize to JSON and flush on the network each element of the `Flux` input. This behavior is handy when the stream is infinite, or when you want to push information to the client as soon as it's available. Note that this has a performance cost, as calling the serializer and flushing multiple times takes resources.