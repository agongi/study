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

### 1. XML-based approach
SpringContext and bean definitions are declared in web.xml and each of config files.

 - web.xml
 - applicationContext.xml
 - api-servlet.xml

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

#### 1.1 Java-based approach
- main or WebApplicationInitializer implementations entry-point
- AppConfig.java
- DispatcherConfig.java

```java
public class MyWebAppInitializer implements WebApplicationInitializer {

   @Override
   public void onStartup(ServletContext container) {
     // Create the Spring application context
     AnnotationConfigWebApplicationContext rootContext =
       new AnnotationConfigWebApplicationContext();
     rootContext.register(AppConfig.class);

     // Manage the lifecycle of the root application context
     container.addListener(new ContextLoaderListener(rootContext));

     // Create the dispatcher servlet's Spring application context
     AnnotationConfigWebApplicationContext dispatcherContext =
       new AnnotationConfigWebApplicationContext();
     dispatcherContext.register(DispatcherConfig.class);

     // Register and map the dispatcher servlet
     ServletRegistration.Dynamic dispatcher =
       container.addServlet("dispatcher", new DispatcherServlet(dispatcherContext));
     dispatcher.setLoadOnStartup(1);
     dispatcher.addMapping("/");
   }
}

@Configuration
public class AppConfig {

    @Bean
    public MyBean myBean() {
        // instantiate, configure and return bean ...
    }
}
```

### 2. Bootstrapped by servlet-container
**SpringServletContainerInitializer** class will be loaded and instantiated and have its onStartup() method invoked by any Servlet 3.0-compliant container during container startup.
```
ServletContainerInitializer <-- SpringServletContainerInitializer

@HandlesTypes(WebApplicationInitializer.class)
```

**@interface HandlesTypes** is used to declare the class types that a ServletContainerInitializer can handle, Servlet 3.0+ containers will automatically scan the classpath for implementations of Spring's **WebApplicationInitializer interface** and provide the set of all such types to the webAppInitializerClasses parameter of this method.

```java
public class MyWebAppInitializer implements WebApplicationInitializer {

   @Override
   public void onStartup(ServletContext container) {
     // ... some configuration
   }
}
```

#### 2.1 Bootstrapped by itself
java -jar Application

```java
public class Application {

  public static void main(String args[]) {
    AbstractApplicationContext container = new ClassPathXmlApplicationContext("applicationContext.xml");

    // ... some configuration

    container.registerShutdownHook();
}
```
