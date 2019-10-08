## 24. Externalized Configuration
Spring Boot properties are loaded in following order:

 - Command line arguments.
 - JNDI attributes from java:comp/env.
 - Java System properties (System.getProperties()).
 - OS environment variables.
 - A RandomValuePropertySource that only has properties in random.*.*
 - Profile-specific application properties outside of your packaged jar (application-{profile}.properties and YAML variants)
 - Profile-specific application properties packaged inside your jar (application-{profile}.properties and YAML variants)
 - Application properties outside of your packaged jar (application.properties and YAML variants).
 - Application properties packaged inside your jar (application.properties and YAML variants).
 - @PropertySource annotations on your @Configuration classes.
 Default properties (specified using SpringApplication.setDefaultProperties).

**Quick reference**

![img-project-layout](https://github.com/agongi/study/blob/master/spring-boot/%2324/images/Screen%20Shot%202015-11-16%20at%2023.43.06.png)

**application.properties**
```java
name=suktae
```

**BootApplication.java**
```java
package com.example;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BootApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootApplication.class, args);
    }
}
```

**BootService1.java**
```java
package com.example.services;

import org.apache.log4j.Logger;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Component
public class BootService1 implements CommandLineRunner {
	final static Logger logger = Logger.getLogger(BootService1.class);

	@Value("${name}")
	private String name;

	@Override
	@Order(1)
	public void run(String... arg0) throws Exception {
		logger.error("@Order(1)");
		logger.error(name);
	}
}
```

**output**
```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.3.0.RELEASE)

2015-11-16 23:45:14.776  INFO 2680 --- [           main] com.example.BootApplication              : Starting BootApplication on suwon-iMac-mac.local with PID 2680 (/Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-demo/target/classes started by suktaechoi in /Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-demo)
2015-11-16 23:45:14.778  INFO 2680 --- [           main] com.example.BootApplication              : No profiles are active
2015-11-16 23:45:14.811  INFO 2680 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@6c9f5c0d: startup date [Mon Nov 16 23:45:14 KST 2015]; root of context hierarchy
2015-11-16 23:45:15.200  INFO 2680 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2015-11-16 23:45:15.207 ERROR 2680 --- [           main] com.example.services.BootService1        : @Order(1)
2015-11-16 23:45:15.207 ERROR 2680 --- [           main] com.example.services.BootService1        : suktae
2015-11-16 23:45:15.207 ERROR 2680 --- [           main] com.example.services.BootService2        : @Order(2)
2015-11-16 23:45:15.207 ERROR 2680 --- [           main] com.example.services.BootService3        : @Order(3)
2015-11-16 23:45:15.208  INFO 2680 --- [           main] com.example.BootApplication              : Started BootApplication in 0.578 seconds (JVM running for 0.89)
2015-11-16 23:45:15.209  INFO 2680 --- [       Thread-1] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@6c9f5c0d: startup date [Mon Nov 16 23:45:14 KST 2015]; root of context hierarchy
2015-11-16 23:45:15.210  INFO 2680 --- [       Thread-1] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

#### 24.3. Application property files
Spring finds application.properties files in following locations.
```
Default search path

1. file:config/
 >> A /config subdir of the current directory

2. file
 >> The current directory

3. classpath:/config
 >> A classpath /config package

4. classpath:
 >> The classpath root
```
>application.yml is also suitable

Customizes files name and location.
```
spring.config.name=myproject
spring.config.location=classpath:/aaa.properties,classpath:/bbb.properties
```

#### 24.4. Profile-specific properties
Profile-specific properties will override default one. The naming rule is **application-{profile}.properties**
```
application-prd.properties
application-stg.properties
application-dev.properties
application-local.properties
```

#### 24.6. Using YAML instead of Properties
###### 24.6.1. Loading YAML
**Two ways to define environment property between .properties an .yml.**
```
environments:
    dev:
        url: http://dev.bar.com
        name: Developer Setup
    prod:
        url: http://foo.bar.com
        name: My Cool App
```

**equivalent to these syntaxes:**
```
environments.dev.url=http://dev.bar.com
environments.dev.name=Developer Setup
environments.prod.url=http://foo.bar.com
environments.prod.name=My Cool App
```

**Here is another example.**
```
my:
   servers:
       - dev.bar.com
       - foo.bar.com
```

**equivalent to these syntaxes:**
```
my.servers[0]=dev.bar.com
my.servers[1]=foo.bar.com
```

###### 24.6.4. YAML shortcomings
YAML can't be loaded via ``@PropertySource`` annotation

#### 24.7. Typesafe Configuration Properties
You can define specific prefix to inject envionment properties in beans.
```java
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {

    private String username;
    private InetAddress remoteAddress;
    // ... getters and setters
}
```

>Getters and Setter are required to inject properties in variables out of beans followed spring DI.

**@EnableConfigurationProperties** annotation is applied to your **@Configuration**, any beans annotated with **@ConfigurationProperties**. Those will be ``automatically`` configured from the Environment properties.

It is also possible to manually define beans in ``@EnableConfigurationProperties`` annotation.
```java
@Configuration
@EnableConfigurationProperties(ConnectionSettings.class)
public class MyConfiguration {
  // ...
}
```

###### 24.7.1 Third-party configuration
**@Bean** is available as well. It is especially useful when you bind 3rd-party properties out of control.

```java
@ConfigurationProperties(prefix = "foo") // used to specified prefix
@Bean
public FooComponent fooComponent() {
    ...
}
```

###### 24.7.3. @ConfigurationProperties Validation
Validates ``@ConfigurationProperties`` class by using javax.validation based on JSR-303.

```java
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {
    @NotNull
    private InetAddress remoteAddress;

    // ... getters and setters
}
```
