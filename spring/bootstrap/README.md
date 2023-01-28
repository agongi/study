## Bootstrap

```
ㅁ Author: suktae.choi
- http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html#beans-beanfactory
- http://www.jcombat.com/spring/spring-container-basics-dispatcher-servlet-and-servlet-listener
- http://stackoverflow.com/questions/18578143/about-multiple-containers-in-spring-framework
- https://www.mkyong.com/servlet/what-is-listener-servletcontextlistener-example/
- http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/SpringServletContainerInitializer.html
```

#### Index

- [Life Cycle](life-cycle)

### By-ServletContainer
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

`ServletContainerInitializer` needs `WEB-INF/services/javax.servlet.ServletContainerInitializer` file which contains entry-point class name:

```properties
com.toy.org.config.BaseInitializer
```

> This is the servlet 3.0+ spec to being looking for a file to start.

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

#### Servlet

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

```java
public class BaseInitializer implements WebApplicationInitializer {
  @Override
  public void onStartup(ServletContext container) {
    // context
    AnnotationConfigWebApplicationContext context = new AnnotationConfigWebApplicationContext();
    context.register(AppConfig.class);
		
    // dispatcher
    DispatcherServlet dispatcherServlet = new DispatcherServlet(context);     
    ServletRegistration.Dynamic registrar = container.addServlet("dispatcher", dispatcherServlet);
    registrar.setLoadOnStartup(1);
    registrar.addMapping("/");
  }	
}
```

#### Spring-Framework

```java
public class BaseInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class<?>[] {BaseConfig.class};
  }

  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class<?>[] {AppConfig.class};
  }

  @Override
  protected String[] getServletMappings() {
    return new String[] {"/"};
  }
}
```

#### Spring-Boot

WebApplicationInitializer is raw-level interface to bootstrap, and It is not enough to initialize spring-boot features. Now boot supports its wrapper class to enable all spring-boot functions also.

```java
public class BaseInitializer extends SpringBootServletInitializer {
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

### By-CLI

#### Spring-Framework

```java
public class Application {
  public static void main(String args[]) {
    AbstractApplicationContext container = new ClassPathXmlApplicationContext("...");

    // ... some configuration
    container.registerShutdownHook();
  }
```

#### **Spring-Boot**

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