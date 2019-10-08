##  11. Developing your first Spring Boot application
#### 11.1. Creating the projdct and POM.xml
Get started in STS. In Package Explorer, right-click, New -> Spring Starter Project with default settings.

```
├── bin
│   ├── application.properties
│   └── com
│       └── example
│           ├── BootFunction1.class
│           ├── BootGradleApplication.class
│           └── BootGradleApplicationTests.class
├── build
├── build.gradle
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
└── src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── example
    │   │           ├── BootFunction1.java
    │   │           └── BootGradleApplication.java
    │   └── resources
    │       └── application.properties
    └── test
        └── java
            └── com
                └── example
                    └── BootGradleApplicationTests.java

16 directories, 13 files
```

The maven POM.xml is showing following.

> Gradle doesn't support parent import. only maven is caring in current version

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.2.7.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
		<!-- Additional lines to be added here... -->
	</dependencies>
</project>
```

#### 11.2. Adding classpath dependencies
Add dependency in POM.xml.
```xml
<dependencies>
  <!-- additional dependencies -->

	<!-- JUnit -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
</dependencies>
```

#### 11.3. Writing the code
###### 11.3.1. main
It can be used to **bootstrap** and launch a Spring application from a Java main method. By default class will perform the following steps to bootstrap your application:
 - Create an appropriate ApplicationContext instance (depending on your classpath)
 - Register a CommandLinePropertySource to expose command line arguments as Spring properties
 - Refresh the application context, loading all singleton beans
 - Trigger any CommandLineRunner beans

###### 11.3.2. CommandLineRunner
If you want access to the raw command line arguments, or you need to `run some specific code` once the SpringApplication has started you can implement the CommandLineRunner interface. `The run(String…​ args) method will be called on all Spring beans implementing this interface.`

```java
DemoApplication.java

@SpringBootApplication
public class BootDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootDemoApplication.class, args);
    }
}
------------------------------------------------------------------------
DemoFunction1.java

@Component
public class BootFunction1 implements CommandLineRunner {
	final static Logger logger = Logger.getLogger(BootFunction1.class);

	@Override
	public void run(String... arg0) throws Exception {
		logger.error("hello node");
	}
}
```

#### 11.4. Running the example
Run the project.
![img-maven-build](https://github.com/agongi/study/blob/master/spring-boot/%2311/images/Screen%20Shot%202015-10-23%20at%2001.02.31.png)

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v1.2.7.RELEASE)

2015-10-25 17:02:38.602  INFO 1160 --- [           main] com.example.BootGradleApplication        : Starting BootGradleApplication on suwon-iMac-mac.local with PID 1160 (/Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-gradle/bin started by suktaechoi in /Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-gradle)
2015-10-25 17:02:38.626  INFO 1160 --- [           main] s.c.a.AnnotationConfigApplicationContext : Refreshing org.springframework.context.annotation.AnnotationConfigApplicationContext@fcd6521: startup date [Sun Oct 25 17:02:38 KST 2015]; root of context hierarchy
2015-10-25 17:02:38.924  INFO 1160 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2015-10-25 17:02:38.932 ERROR 1160 --- [           main] com.example.BootFunction1                : hello node
2015-10-25 17:02:38.933  INFO 1160 --- [           main] com.example.BootGradleApplication        : Started BootGradleApplication in 0.473 seconds (JVM running for 0.897)
2015-10-25 17:02:38.934  INFO 1160 --- [       Thread-1] s.c.a.AnnotationConfigApplicationContext : Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@fcd6521: startup date [Sun Oct 25 17:02:38 KST 2015]; root of context hierarchy
2015-10-25 17:02:38.935  INFO 1160 --- [       Thread-1] o.s.j.e.a.AnnotationMBeanExporter        : Unregistering JMX-exposed beans on shutdown
```

#### 11.5. Creating an executable jar
Add dependency in POM.xml.
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```
Set maven profile: clean package to generate .jar
![img-spring-boot-app](https://github.com/agongi/study/blob/master/spring-boot/%2311/images/Screen%20Shot%202015-10-23%20at%2001.01.26.png)

```
Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO]
[INFO] --- maven-jar-plugin:2.5:jar (default-jar) @ demo ---
[INFO] Building jar: /Users/suktaechoi/Documents/OneDrive/workspace/sts/boot-demo/target/demo-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:1.2.7.RELEASE:repackage (default) @ demo ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.067 s
[INFO] Finished at: 2015-10-25T21:55:01+09:00
[INFO] Final Memory: 23M/309M
[INFO] ------------------------------------------------------------------------
```
