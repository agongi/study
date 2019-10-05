## Spring Bootstrap

```
ㅁ Author: suktae.choi
ㅁ References:
- http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html#beans-beanfactory
- http://www.jcombat.com/spring/spring-container-basics-dispatcher-servlet-and-servlet-listener
- http://stackoverflow.com/questions/18578143/about-multiple-containers-in-spring-framework
- https://www.mkyong.com/servlet/what-is-listener-servletcontextlistener-example/
- http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/SpringServletContainerInitializer.html
```

#### Index

- [Spring Flow](spring-flow)

### Config

#### XML-Based
```xml
<listener>
     <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<context-param>
     <param-name>contextConfigLocation</param-name>
     <param-value>classpath:applicationContext.xml</param-value>
</context-param>

<servlet>
      <servlet-name>api-servlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:servlet/api-servlet.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
	    <servlet-name>api-servlet</servlet-name>
	    <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### Java-Based
```java
public class MyWebAppInitializer implements WebApplicationInitializer {
   @Override
   public void onStartup(ServletContext container) {
     // Create the Spring application context
     AnnotationConfigWebApplicationContext rootContext = new AnnotationConfigWebApplicationContext();
     rootContext.register(AppConfig.class);

     container.addListener(new ContextLoaderListener(rootContext));

     AnnotationConfigWebApplicationContext dispatcherContext = new AnnotationConfigWebApplicationContext();
     dispatcherContext.register(DispatcherConfig.class);

     ServletRegistration.Dynamic dispatcher = container.addServlet("dispatcher", new DispatcherServlet(dispatcherContext));
     dispatcher.setLoadOnStartup(1);
     dispatcher.addMapping("/");
   }
}
```

### Bootstrap
#### By Servlet Container
**ServletContainerInitializer** class will be loaded, instantiated and have its onStartup() method invoked `by any Servlet 3.0+ container` in bootstrap.

```java
ServletContainerInitializer (javax) 
-> @HandlesTypes(WebApplicationInitializer.class) SpringServletContainerInitializer (spring)
-> WebApplicationInitializer
	-> AbstractDispatcherServletInitializer ->
	-> AbstractAnnotationConfigDispatcherServletInitializer	 // spring-framework
	
	and/or
	
  -> SpringBootServletInitializer // spring-boot
```

Class that inherits `SerlvetContainerInitializer` may be annotated `@HandleTypes` to point out next chains of initialize.

```java
/**
 * <p>Implementations of this interface may be annotated with
 * {@link javax.servlet.annotation.HandlesTypes HandlesTypes}, in order to
 * receive (at their {@link #onStartup} method) the Set of application
 * classes that implement, extend, or have been annotated with the class
 * types specified by the annotation.
 */
@HandlesTypes(WebApplicationInitializer.class)
public class SpringServletContainerInitializer implements ServletContainerInitializer {
	// ..
}
```

Now Servlet 3.0+ containers will automatically scan the classpath for implementations of Spring's `WebApplicationInitializer` interface.

**Spring-Framework**

```java
public class MyWebAppInitializer implements WebApplicationInitializer {
  @Override
  public void onStartup(ServletContext container) {
    AnnotationConfigWebApplicationContext rootContext = new AnnotationConfigWebApplicationContext();	// annotation-based config
    rootContext.register(AppConfig.class);
  }
}

@Configuration
@Import(...)
public class AppConfig {
  @Bean
  public MyBean myBean() {
    return new MyBean();
  }
}
```

**Spring-Boot**

WebApplicationInitializer is raw-level interface to bootstrap, and It is not enough to initialize spring-boot features. Now boot supports its wrapper class to enable all spring-boot functions also.

```java
public class BootInitializer extends SpringBootServletInitializer {
  @Override
  public void onStartup(ServletContext servletContext) throws ServletException {
    super.onStartup(servletContext);
  }

  @Override
  protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
    return builder.sources(ToyConfig.class);	// @SpringBootApplication class
  }
}
```

#### By Itself

**Spring-Framework**

```java
public class Application {
  public static void main(String args[]) {
    AbstractApplicationContext container = new ClassPathXmlApplicationContext("...");

    // ... some configuration
    container.registerShutdownHook();
  }
```

**Spring-Boot**

```java
@Import(ToyConfig.class)
@SpringBootApplication
public class ToyApplication {
  /**
	 * application main
	 */
  public static void main(String[] args) {
    SpringApplication.run(ToyApplication.class, args);
  }
}
```