## 25. Profiles
**Set profiles in annotation.**

```java
@Configuration
@Profile("production")
public class ProductionConfiguration {
    // ...
}
```

**in properties.**
```
spring.profiles.active: prd
```

#### 25.1. Adding active profiles
**Quick reference**

![img-project-layout](https://github.com/agongi/study/blob/master/spring-boot/%2325/images/Screen%20Shot%202015-11-17%20at%2000.34.57.png)

**application.properties**
```
# common
name: suktae-default

# profile
spring.profiles.active: prd
```

**application-{PROFILE}.properties**
```
name: suktae-prd

spring.profiles.include: prddb,prdmq
```

**application-prddb.properties**
```
db: db-prd
```

**application-prdmq.properties**
```
mq: mq-prd
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
	@Value("${db}")
	private String db;
	@Value("${mq}")
	private String mq;

	@Override
	@Order(1)
	public void run(String... arg0) throws Exception {
		logger.error("@Order(1)");
		logger.error(name);
		logger.error(db);
		logger.error(mq);
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
:: Spring Boot ::        (v1.3.1.RELEASE)

@Order(1)
suktae-prd
db-prd
mq-prd
@Order(2)
@Order(3)
Started BootApplication in 2.393 seconds (JVM running for 3.022)
```
