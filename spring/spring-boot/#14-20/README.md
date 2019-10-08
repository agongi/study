##  14. Structuring your code
#### 14.1. Locating the main application class
Locate your main application class in a root package above other classes

```
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           ├── BootGradleApplication.java
    │   │           └── service
    │   │               └── BootService1.java
    │   └── resources
    │       └── application.properties
```

BootGradleApplication.java file would declare the main method, along with the basic `@Configuration`.
>Main class should be located in a root of packages. Then It would automatically search/inject all declared beans in runtime.

```java
@SpringBootApplication
public class BootGradleApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootGradleApplication.class, args);
    }
}
```
**@SpringBootApplication** is equivalent to following annotations.

 - `@Configuration`: tags the class as a **source of bean definitions** for the application context.
 - `@EnableAutoConfiguration`: tells Spring Boot to start **adding beans based on classpath settings, other beans, and various property settings.** Normally you would add @EnableWebMvc for a Spring MVC app, but Spring Boot adds it automatically when it sees spring-webmvc on the classpath. This flags the application as a web application and activates key behaviors such as setting up a DispatcherServlet
 - `@ComponentScan`: tells Spring to **look for other components**, configurations, and services in the the hello package, allowing it to find the HelloController.

## 15. Configuration classes
The `@Import` annotation can be used to import additional configuration classes.

The XML based configuration can be started with a `@Configuration` class. You can then use an additional `@ImportResource` annotation to load XML configuration files.

>Always try to use the equivalent Java-base configuration if possible.

## 16. Auto-configuration
Spring Boot auto-configuration attempts to automatically configure your Spring application based on the jar dependencies that you have added by adding the `@EnableAutoConfiguration`.

You can also exclude specific-dependencies with exclude attribute.
```java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {

}
```

## 17. Spring Beans and dependency injection
You are free to use any of Spring Framework techniques to define beans and DI. Simply, `@ComponentScan` to find your beans and `@Autowired` constructor injection works well.

If you structure your code as suggested above **(locating your application class in a root package)**, you can add `@ComponentScan` without any arguments. All of your application components **(@Component, @Service, @Repository, @Controller etc.)** will be automatically registered as Spring Beans.

## 19. Running your application
IDE supports convenience way to run spring-boot application.

![img-run-spring-boot](https://github.com/agongi/study/blob/master/spring-boot/%2314-20/images/Screen%20Shot%202015-11-09%20at%2023.42.13.png)
