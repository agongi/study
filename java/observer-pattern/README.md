## Observer pattern
Observer pattern is listening interesting changes from particular instance.

```
@author: suktae.choi
- http://javarevisited.blogspot.com/2011/12/observer-design-pattern-java-example.html
- http://egloos.zum.com/iilii/v/3902774
```

#### WatcherImpl.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
@AllArgsConstructor
@NoArgsConstructor
public class WatcherImpl implements Watcher {
  private ArrayList<Observer> observers = new ArrayList<>();
  private String value;

  @Override
  public void add(Observer observer) {
    this.observers.add(observer);
  }

  @Override
  public void remove(Observer observer) {
    this.observers.remove(observer);
  }

  @Override
  public void notify(String value) {

    for(Observer observer : observers) {
      log.debug("value:{} -> [{}] ", value, observer.getClass().getSimpleName());
      observer.handleNotify(value);
    }
  }

  public String getValue() {
    return value;
  }

  public void setValue(String value) {
    this.value = value;
    notify(this.value);
  }
}
```

#### ObserverImpl.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class ObserverImpl1 implements Observer {
  @Override
  public void handleNotify(String value) {
    log.debug("ObserverImpl1 notified: [{}]", value);
  }
}
```

#### Application.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class Application {
  public static void main(String[] args) {
    Watcher watcher = new WatcherImpl();

    Observer observer1 = new ObserverImpl1();
    Observer observer2 = new ObserverImpl2();

    // subscription
    watcher.add(observer1);
    watcher.add(observer2);

    // value changed, send event
    watcher.setValue("suktae");
  }
}
```

#### Output
```java
2016-08-28 22:02:42 [main] DEBUG c.l.l.model.watcher.impl.WatcherImpl - value:suktae -> [ObserverImpl1]
2016-08-28 22:02:42 [main] DEBUG c.l.l.m.observer.impl.ObserverImpl1 - ObserverImpl1 notified: [suktae]
2016-08-28 22:02:42 [main] DEBUG c.l.l.model.watcher.impl.WatcherImpl - value:suktae -> [ObserverImpl2]
2016-08-28 22:02:42 [main] DEBUG c.l.l.m.observer.impl.ObserverImpl2 - ObserverImpl2 notified: [suktae]

Process finished with exit code 0
```
