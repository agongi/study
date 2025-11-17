# Context Provider
```
https://stackoverflow.com/questions/21827548/spring-get-current-applicationcontext
```
```java
public class ApplicationContextProvider implements ApplicationListener<ApplicationContextInitializedEvent> {
    private static ApplicationContext applicationContext;

    @Override
    public void onApplicationEvent(ApplicationContextInitializedEvent event) {
        ApplicationContextProvider.applicationContext = event.getApplicationContext();
    }

    public static ApplicationContext getContext() {
        return applicationContext;
    }

    public static <T> T getBean(Class<T> requiredType) {
        return applicationContext.getBean(requiredType);
    }
}
```