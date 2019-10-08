## ApplicationEvent

```
ㅁ Author: suktae.choi
ㅁ References:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
```

### Event Object

```java
public class UserEvent implements ApplicationEvent {
	// fields, getter/setter
}
```

### Publisher

```java
// over interface
public class UserEventPublisher implements ApplicationEventPublisherAware {
  private final ApplicationEventPublisher publisher1;
  @Autowire
  private ApplicationEventPublisher publisher2;
  
  // @Autowire context
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
public class UserEventPublisher implements ApplicationListener<UserEvent> {
  @Override
  public void onApplicationEvent(UserEvent event) {
    // .. 
  }

  @EventListener
  public void onEvent(UserEvent event) {
    // ..
  }

  // after tx, this listener invoked
  @TransactionalEventListener
  public void onEventTx(UserEvent event) {
	// ..
  }
}
```

