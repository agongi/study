## 23. SpringApplication
#### 23.2. Customizing SpringApplication
Customizes SpringApplication instead of default.

```java
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(MySpringConfiguration.class);
    app.setShowBanner(false);
    app.run(args);
}
```
>The constructor arguments are configuration sources for spring beans. Mostly these will be references to ``@Configuration`` classes, but they could also be references to XML configuration or to packages.

#### 23.4. Application events and listeners
Defines listener in **SpringApplication.addListeners(…​)** method. Application events are sent in the following order.
 - ``ApplicationStartedEvent`` is sent at the start of a run, but before any processing except the registration of listeners and initializers.
 - ``ApplicationEnvironmentPreparedEvent`` is sent when the Environment to be used in the context is known, but before the context is created.
 - ``ApplicationPreparedEvent`` is sent just before the refresh is started, but after bean definitions have been loaded.
 - ``ApplicationFailedEvent`` is sent if there is an exception on startup.

#### 23.5. Web environment
Servlet API like DispatcherServlet and other web components are activated by default. To disable, explicitly call method.
```java
public static void main(String[] args) {
    SpringApplication app = new SpringApplication(MySpringConfiguration.class);
    app.setWebEnvironment(false);
    app.run(args);
}
```

#### 23.6. Using the CommandLineRunner
Run application with commandline arguments or simply invoke specific function once, implements ``CommandLineRunner`` interface.
```java
import org.springframework.boot.*
import org.springframework.stereotype.*

@Component
public class FunctionA implements CommandLineRunner {
    @Override
    public void run(String... args) {
        // Do something...
    }
}
```
Executes multiple commandLineRunners in specific order.
```java
import org.apache.log4j.Logger;
import org.springframework.boot.CommandLineRunner;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;
------------------------------------------------------------------------
@Component
public class BootService1 implements CommandLineRunner {
	final static Logger logger = Logger.getLogger(BootService1.class);

	@Override
	@Order(1)
	public void run(String... arg0) throws Exception {
		logger.error("@Order(1)");
	}				
}
------------------------------------------------------------------------
@Component
public class BootService2 implements CommandLineRunner {
	final static Logger logger = Logger.getLogger(BootService1.class);

	@Override
	@Order(2)
	public void run(String... arg0) throws Exception {
		logger.error("@Order(2)");
	}				
}
------------------------------------------------------------------------
@Component
public class BootService3 implements CommandLineRunner {
	final static Logger logger = Logger.getLogger(BootService1.class);

	@Override
	@Order(3)
	public void run(String... arg0) throws Exception {
		logger.error("@Order(3)");
	}				
}
```
Here is result.
```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.2.7.RELEASE)

2015-11-12 00:32:41.658  INFO 6292 --- [           main] com.example.BootGradleApplication        : Starting BootGradleApplication on suwon-iMac-mac.local with PID 6292 (/Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-gradle/bin started by suktaechoi in /Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-gradle)
2015-11-12 00:32:41.685  INFO 6292 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@fcd6521: startup date [Thu Nov 12 00:32:41 KST 2015]; root of context hierarchy
2015-11-12 00:32:42.007  INFO 6292 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2015-11-12 00:32:42.015 ERROR 6292 --- [           main] com.example.service.BootService1         : @Order(1)
2015-11-12 00:32:42.015 ERROR 6292 --- [           main] com.example.service.BootService2         : @Order(2)
2015-11-12 00:32:42.015 ERROR 6292 --- [           main] com.example.service.BootService3         : @Order(3)
2015-11-12 00:32:42.016  INFO 6292 --- [           main] com.example.BootGradleApplication        : Started BootGradleApplication in 0.488 seconds (JVM running for 0.827)
2015-11-12 00:32:42.017  INFO 6292 --- [       Thread-1] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@fcd6521: startup date [Thu Nov 12 00:32:41 KST 2015]; root of context hierarchy
2015-11-12 00:32:42.017  INFO 6292 --- [       Thread-1] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```
