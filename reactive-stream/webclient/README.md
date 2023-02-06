## WebClient

```
@author: suktae.choi
- https://stackoverflow.com/questions/46235512/how-to-set-a-timeout-in-spring-5-webflux-webclient
- https://resilience4j.readme.io/docs/getting-started-3
```

## Config

```java
@Configuration
public class CustomWebClientConfig {

  private static final int CONNECTION_TIMEOUT_MILLIS = 2_000;
  private static final int READ_TIMEOUT_MILLIS = 2_500;

  @Bean
  public WebClient webClient(
    @Value("${api.url}") String url) {

    return WebClient.builder()
      .baseUrl(url)
      .defaultHeaders(headers -> {
        headers.add(HttpHeaders.ACCEPT, MediaType.APPLICATION_JSON_VALUE);
      })
      .clientConnector(clientHttpConnector())
      .build();
  }

  ReactorClientHttpConnector clientHttpConnector() {
    return clientHttpConnector(CONNECTION_TIMEOUT_MILLIS, READ_TIMEOUT_MILLIS);
  }

  ReactorClientHttpConnector clientHttpConnector(int connectionTimeout, int readTimeout) {
    TcpClient tcpClient = TcpClient.create()
      .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, connectionTimeout)
      .doOnConnected(conn -> conn
				// https://www.hungrydiver.co.kr/bbs/detail/develop?id=7&scroll=comment
        .addHandlerLast(NettyPipeline.ChunkedWriter, new ChunkedWriteHandler())
				.addHandlerLast(new ReadTimeoutHandler(readTimeout, TimeUnit.MILLISECONDS))
// .addHandlerLast(new WriteTimeoutHandler(WRITE_TIMEOUT_MILLIS, TimeUnit.MILLISECONDS)) // 필요시 정의
			);
    
    return new ReactorClientHttpConnector(HttpClient.from(tcpClient));
  }
}
```

## Test

```java
@Test
public void apiTest(@Autowire WebClient webClient) {
  // given
  String id = "1234";

  // when
  User response = getUser(webClient, id)
    .blockOptional().orElse(null);

  // then
  assertThat(response).isNotNull();
  assertThat(response.getId()).isEqualTo(id);
}

Mono<User> getUser(WebClient webClient, String id) {
  return webClient.get()
    .uri(uriBuilder -> uriBuilder.path("/user")
         .queryParam("id", id)
         .build())
    .retrieve()
    .bodyToMono(User.class)
    .doOnSuccess(res -> {
      log.debug("res={}", res);
    })
    .onErrorMap(e -> {
      log.error("AccountApiClient#getNextSalesDay", e);
      // throws
    });
}
```

