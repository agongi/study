## Spring Bootstrap

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.01
ㅁ References:
 - http://docs.spring.io/spring/docs/current/spring-framework-reference/html/beans.html#beans-beanfactory
 - http://www.jcombat.com/spring/spring-container-basics-dispatcher-servlet-and-servlet-listener
 - http://stackoverflow.com/questions/18578143/about-multiple-containers-in-spring-framework
 - https://www.mkyong.com/servlet/what-is-listener-servletcontextlistener-example/
 - http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/SpringServletContainerInitializer.html
```

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
### By Servlet Container
**ServletContainerInitializer** class will be loaded and instantiated and have its onStartup() method invoked by any Servlet 3.0+ container during container startup.
```
ServletContainerInitializer(javax) <-- SpringServletContainerInitializer <-- WebApplicationInitializer <--
... <-- AbstractAnnotationConfigDispatcherServletInitializer

@HandlesTypes(WebApplicationInitializer.class)
```

**@Interface HandlesTypes** is used to declare the class types that a ServletContainerInitializer can handle, Servlet 3.0+ containers will automatically scan the classpath for implementations of Spring's **WebApplicationInitializer interface** and provide the set of all such types to the webAppInitializerClasses parameter of this method.

```java
public class MyWebAppInitializer implements WebApplicationInitializer {
   @Override
   public void onStartup(ServletContext container) {
     AnnotationConfigWebApplicationContext rootContext = new AnnotationConfigWebApplicationContext();
     rootContext.register(AppConfig.class);
   }
}

@Configuration
public class AppConfig {
    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```

#### By Itself
```java
public class Application {
  public static void main(String args[]) {
    AbstractApplicationContext container = new ClassPathXmlApplicationContext("applicationContext.xml");

    // ... some configuration
    container.registerShutdownHook();
}
```
