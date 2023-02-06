## @EventListener - ApplicationListener

```
@author: suktae.choi
- https://www.baeldung.com/spring-events
```

## Event

```java
public class UserEvent implements ApplicationEvent {
  // fields, getter/setter
}
```

***

## Publisher

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

Publisher 는 제공되는 Annotation 방식이 없다.

- 그에 대한 개선정리 https://supawer0728.github.io/2018/03/24/spring-event/

***

## Listener - @EventListener

```java
@Component
public class UserEventListener {
  @EventListener
  public void onEventAll(UserEvent event) {
    // 모든 publisher 의 이벤트를 받음
  }

  @EventListener(UserEvent.class)
  public void onUserEvent(UserEvent event) {
    // 해당 타입의 이벤트만 받음 (topic)
  }

  @EventListener(condition = "#UserEvent.isAdult")
  public void onUserEventIfAdult(UserEvent event) {
    // conditional
  }

  // after tx, this listener invoked
  @TransactionalEventListener
  public void onEventTx(UserEvent event) {
    // ..
  }
}
```

## Listener - ApplicationListener

```java
@Component or META-INF/spring.factories
public class UserEventListener implements ApplicationListener<UserEvent> {
  @Override
  public void onApplicationEvent(UserEvent event) {
    // 해당 타입의 이벤트만 받음 (topic)
  }
}
```

or

```java
public class UserEventListener implements ApplicationListener<UserEvent> {
  @Override
  public void onApplicationEvent(UserEvent event) {
    // 해당 타입의 이벤트만 받음 (topic)
  }
}

// META-INF/spring.factories
org.springframework.context.ApplicationListener=\
UserEventListener
```

