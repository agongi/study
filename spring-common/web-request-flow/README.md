## Web Request Flow
This section explains whole life-cycle of HTTP Request in view of spring perspective.

> Client -> Coyote -> Catalina -> filter -> DispatcherServlet -> HandlerMapping -> HandlerExecutionChain -> HandlerAdapter -> @Controller -> @Service -> @Repository -> RDB -> HandlerExecutionChain -> ViewResolver

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.02 ~
ㅁ References:
 - http://duckranger.com/2012/04/spring-mvc-dispatcherservlet
 - https://blog.outsider.ne.kr/902
```

<img src="https://github.com/agongi/study/blob/master/spring-common/web-request-flow/images/Screen%20Shot%202016-08-20%20at%2021.33.02.png">

1) Tomcat **Coyote** is listening port 80 or 443 for any incoming packets.

2) Coyote delegates incomings to **Catalina** Engine. (== Servlet-Container)

3) Packets are sent to **filter-chaining** which are pre-defined in web.xml and loaded in servlet-container's bootstrap time for updating contents such as encoding converter or authentication logics in order.

4) After completion of filter-chaining, Packets reach servlet, in spring **DispatcherServlet**.

5) DispatcherServlet delegates method finding to **HandlerMapping**. HandlerMapping will find matching URL-pattern over `@Controller` and `@RequestMapping` annotation. It sent back to DispatcherServlet the method name.

6) DispatcherServlet delegates interceptor finding to **HandlerExecutionChain**.

7) If it gets interceptors that is matched in given URL pattern, DispatcherServlet sends HTTP Request to interceptor-chain and invokes **preHandle()** method.

8) DispatcherServlet delegates execution of method to **HandlerAdapter**.

9) HandlerAdapter invokes @Controller.

10) `@Controller - @Service - @Repository - RDB` are our boilerplate web application flow.

11) If interceptors are given, then DispatcherServlet invokes user-defined interceptor implementations's **postHandle()** method.

12) If `@ResponseBody` is not defined in return type in `@Controller`, then DispatcherServlet delegates view finding to **ViewResolver** and renders **view**.

12-1) If `@ResponseBody` is defined in return type in `@Controller`, then DispatcherServlet serializes POJO object to JSON or XML based response

13) DispatcherServlet responds to client.
