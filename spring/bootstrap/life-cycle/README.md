## Life Cycle

```
ㅁ Author: suktae.choi
- http://duckranger.com/2012/04/spring-mvc-dispatcherservlet
- https://blog.outsider.ne.kr/902
- http://devbox.tistory.com/entry/Spring-webxml-%EA%B8%B0%EB%B3%B8-%EC%84%A4%EC%A0%95
- http://www.javajigi.net/display/JAVA/Servlet+Life+Cycle
```

<img src="images/Screen%20Shot%202017-06-17%20at%2017.19.48.png">

### Cores
#### Servlet
The instance that Handles request from client to respond

#### Servlet Context
The simple Map<String, ServletContext> that manages all servlet instances

```java
public void contextInitialized(ServletContextEvent event) {
   context = event.getServletContext();

   BookDB bookDB = new BookDB();

   // this servlet context is accessible from all logics
   context.setAttribute("bookDB", bookDB);
 }
```

#### Servlet Container
The Servlet-Container (tomcat, jetty) that handles servlet's life-cycle in servletContext

#### Application Context
The simple Map<String, Object> that manages all spring bean instances

#### Spring IoC Container
The Spring-Container is the Dispatcher Servlet that handles all spring-bean's life-cycle in applicationContext

***

### Life Cycle
- Tomcat **Coyote** is listening port 80 or 443 for any incoming packets.
- Coyote delegates incomings to **Catalina** Engine. (== Servlet Container)
- **Servlet Container** bootstraps itself and reads `web.xml`.
  - ServletContext is created
  - If listener is defined, create listener instance and put in servletContext.
    - **ContextLoaderListener class** implements ServletContextListener that receives event servletContext lifecycle changes
    - contextInitialized() is invoked and initializes **ApplicationContext** by default in `classpath:applicationContext.xml`
    - All bean are instantiated and stored in applicationContext
  - If servlet is defined, create servlet instance and put in servletContext.
    - **DispatcherServlet class** implements ApplicationContextAware that is notified applicationContext when it runs
    - It injects applicationContext in instance field
    - It creates **WebApplicationContext** by default `{servlet-name}-servlet.xml`
    - All Spring related configuration are loading
  - If filter is defined, create instance and managed in servletContext
- Packets are sent to **filter-chaining** for updating contents such as encoding converter or authentication logics in order.
- After completion of filter-chaining, Packets reach servlet, in spring **DispatcherServlet**.
- DispatcherServlet delegates method finding to **HandlerMapping**. HandlerMapping will find matching URL-pattern over `@Controller` and `@RequestMapping` annotation. It sent back to DispatcherServlet the method name.
- DispatcherServlet delegates interceptor finding to **HandlerExecutionChain**.
- If it gets interceptors that is matched in given URL pattern, DispatcherServlet sends HTTP Request to interceptor-chain and invokes **preHandle()** method.
- DispatcherServlet delegates execution of method to **HandlerAdapter**.
- HandlerAdapter invokes @Controller.
- `@Controller - @Service - @Repository - RDB` are our boilerplate web application flow.
- If interceptors are given, then DispatcherServlet invokes user-defined interceptor implementations's **postHandle()** method.
- If `@ResponseBody` is not defined in return type in `@Controller`, then DispatcherServlet delegates view finding to **ViewResolver** and renders **view**.
- If `@ResponseBody` is defined in return type in `@Controller`, then DispatcherServlet serializes POJO object to JSON or XML based response
- DispatcherServlet responds to client.
