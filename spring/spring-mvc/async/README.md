## Async

```
@author: suktae.choi
```

### Response

- DeferredResult
- CompletableFuture
- ListenableFuture

### ResponseEmitter

- ResponseBodyEmitter: send multiple-view at one response
  - Transfer-Encoding: chunked
- SseEmitter: split response as event-stream
  - Content-Type: text/event-stream
  - Transfer-Encoding: chunked

### WebSocket

- TextWebSocketHandler
- BinaryWebSocketHandler

```java
@Configuration
@EnableWebSocket
public class SocketConfig implements WebSocketConfigurer {
  @Bean
  public TextSocketHandler textSocketHandler() {
    return new TextSocketHandler();
  }

  @Override
  public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
    registry.addHandler(textSocketHandler(), "/web-socket");
  }
}
```

```java
public class TextSocketHandler extends TextWebSocketHandler {
  @Override
  protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
    String msg = message.getPayload();
    session.sendMessage(new TextMessage(msg));
  }
}
```

