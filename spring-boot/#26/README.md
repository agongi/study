## 26. Logging
Spring Boot supports default logging-framework, But I prefer to use log4j2 (updation of log4j). This is lightweight, most popular and being familiar to use.

Here is guide of it.

**Quick reference**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2326/images/Screen%20Shot%202015-11-19%20at%2000.41.33.png" width="50%">

**pom.xml**
```xml
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.3.0.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter</artifactId>
		    <exclusions>
		        <exclusion>
		            <groupId>org.springframework.boot</groupId>
		            <artifactId>spring-boot-starter-logging</artifactId>
		        </exclusion>
		    </exclusions>
		</dependency>

		<dependency>
		    <groupId>org.springframework.boot</groupId>
		    <artifactId>spring-boot-starter-log4j2</artifactId>
		</dependency>
	</dependencies>

</project>
```

**log4j2.xml (one of the examples. you can customize what you want in pattern section)**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
  <Appenders>
    <File name="file" fileName="app.log">
      <PatternLayout>
        <Pattern>%d %p %c{1.} [%t] %m %ex%n</Pattern>
      </PatternLayout>
    </File>
    <Console name="STDOUT" target="SYSTEM_OUT">
      <PatternLayout pattern="%m%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Root level="trace">
      <AppenderRef ref="file" level="DEBUG"/>
      <AppenderRef ref="STDOUT" level="INFO"/>
    </Root>
  </Loggers>
</Configuration>
```

**BootService1.java**
```java
package com.example.services;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Component
public class BootService1 implements CommandLineRunner {
	private static final Logger logger = LogManager.getLogger(BootService1.class);

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
 :: Spring Boot ::        (v1.3.0.RELEASE)

Starting BootApplication on suwon-iMac-mac.local with PID 6136 (/Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-demo/target/classes started by suktaechoi in /Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-demo)
The following profiles are active: prddb,prdmq,prd
Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@1725dc0f: startup date [Thu Nov 19 00:39:15 KST 2015]; root of context hierarchy
Registering beans for JMX exposure on startup
@Order(1)
suktae-prd
db-prd
mq-prd
@Order(2)
@Order(3)
Started BootApplication in 0.628 seconds (JVM running for 1.102)
```
