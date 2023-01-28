## ThreadLocal in Spring Bean

```
ㅁ Author: suktae.choi
- https://stackoverflow.com/questions/37332219/questions-about-using-threadlocal-in-a-spring-singleton-scoped-service
```

Typically ThreadLocal is declared `private static final` as a class field for **Per-Thread not Per-Thread, Per-Instance**.

But what happened it is declared instance field in spring singleton-scoped bean?

```java
@Service
public class Service {
    // declared as instance field
    private final ThreadLocal<UserInfo> userInfo = new ThreadLocal<>();

    public void doA() {
        // finds user info
        userInfo.set(new UserInfo(userId, name));
        doB();
        doC();
    }

    private void doB() {
        // needs user info
        UserInfo userInfo = userInfo.get();
    }

    private void doC() {
        // needs user info
        UserInfo userInfo = userInfo.get();
    }
}
```

ThreadLocal is a field of singleton-scoped instance of spring bean, It means It never be instantiated again in bean's life-cycle in contrast to normal java instance.

Plain java instance is instantiated every time in needs as long as instance fields. This causes **Per-Thread, Per-Instance.**

So there is no reason to leave it static.
