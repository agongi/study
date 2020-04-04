## ApplicationEvent

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.baeldung.com/spring-events
```

## Flow

### Event

```java
public class UserEvent implements ApplicationEvent {
	// fields, getter/setter
}
```

### Publisher

```java
public class UserEventPublisher implements ApplicationEventPublisherAware {
  private final ApplicationEventPublisher publisher1;
  @Autowire
  private ApplicationEventPublisher publisher2;
	@Autowire
  private ApplicationContext context;  
  
  @Override
  public void setApplicationEventPublisher(
	  ApplicationEventPublisher publisher) {
		
    this.publisher1 = publisher;
  }
  
  public void send() {
    publisher1.publishEvent(new UserEvent("name changed"));
    publisher2.publishEvent(new UserEvent("name changed"));
    context.publishEvent(new UserEvent("name changed"));
  }
}
```

### Listener

```java
public class UserEventListener implements ApplicationListener<UserEvent> {
  @Override
  public void onApplicationEvent(UserEvent event) {
    // 모든 publisher 의 이벤트를 받음
  }

  @EventListener
  public void onEvent(UserEvent event) {
    // 모든 publisher 의 이벤트를 받음
  }

	@EventListener(UserEvent.class)
  public void onEvent(UserEvent event) {
    // 해당 타입의 이벤트만 받음 (topic)
  }

  // after tx, this listener invoked
  @TransactionalEventListener
  public void onEventTx(UserEvent event) {
    // ..
  }
}
```

ApplicationContext 는 ApplicationEventPublisher 를 상속하고있다. 그래서 #publishEvent 는 ApplicationContext 로 전달되고

매칭되는 listener 로 메세지를 전달한다.

> Listener 는 빈으로 등록해야 ctx 에서 매칭이 가능하다.

매칭되는 Listener 의 기준은 아래와같다:

- ApplicationListener#onApplicationEvent method
- @EventListener 가 선언된 method
- @EventListener(UserEvent.class) 로 발행된 event 와 동일한 타입을 선언한 method

## AOP

listener 는 annotation 이 있는데, publisher 는 아직 없다. 깔끔한 비지니스 로직을 원하면 send 를 AOP 로 감싸는 것도 방법이다.

- links https://supawer0728.github.io/2018/03/24/spring-event/

