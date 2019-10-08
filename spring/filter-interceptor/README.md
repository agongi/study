## Filter vs Interceptor
Filter is invoked **before dispatcherServlet** but interceptor handles **after it.**

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.17
ㅁ References:
 - http://changpd.blogspot.kr/2013/03/spring.html
 - http://dev-eido.tistory.com/entry/Interceptor-filter-AOP%EC%9D%98-%EC%B0%A8%EC%9D%B4
 - http://javacan.tistory.com/entry/58
```

<img src="https://github.com/agongi/study/blob/master/spring-common/filter-interceptor/images/89101625_26c5be9fd9.jpg" width="75%">

### Filter
It is invoked in servlet container(web.xml) managed by WAS regardless spring container `before dispatcher servlet`.

```xml
<filter>
    <filter-name>FILTER_NAME</filter-name>
    <filter-class>com.sec.filter.SessionFilter</filter-class>  <!-- user defined class -->
</filter>
<filter-mapping>
    <filter-name>FILTER_NAME</filter-name>
    <url-pattern>/*</url-pattern>       <!-- url pattern -->
    <url-pattern>*.html</url-pattern>   <!-- extension pattern -->
</filter-mapping>
```

Each filter methods are invoked in following conditions:
- init(): initialize
- doFilter(): before/after (like @Around in AOP)
- destroy(): destroy

```java
/**
 * @author suktae.choi
 */
public class SessionFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // before

        chain.doFilter(request, response);

        // after
    }

    @Override
    public void destroy() {

    }
}
```

#### Use spring bean in filter
- Define <filter-class> **org.springframework.web.filter.DelegatingFilterProxy** </filter-class>
- Use the same name defined in <filter-name>
- Use @Autowired as normal spring bean

```xml
<filter>
  <filter-name>myfilter</filter-name>
  <filter-class>
    org.springframework.web.filter.DelegatingFilterProxy
  </filter-class>
</filter>
<filter-mapping>
  <filter-name>myfilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

```java
/**
 * @author suktae.choi
 */
@Component("myfilter")
public class SessionFilter implements Filter {
    @Autowired
    UserService userService;

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // before

        chain.doFilter(request, response);

        // after
    }

    @Override
    public void destroy() {

    }
}
```

### Interceptor
It is invoked `after dispatcher servlet` in defined order. (e.g. A > B > C)

```xml
<mvc:interceptors>
   <mvc:interceptor>
      <mvc:mapping path="/api1/*" />  
      <mvc:mapping path="/api2/*" />  
      <mvc:mapping path="/api3/*" />  
      <bean class="A" />
    </mvc:interceptor>

    <mvc:interceptor>
      <bean class="B" />
    </mvc:interceptor>

    <mvc:interceptor>
      <bean class="C" />
    </mvc:interceptor>
</mvc:interceptors>
```

Each interceptor methods are invoked in following conditions:
- preHandle(): before @Controller
- postHandle(): After @Controller, Before view
- afterCompletion(): After view (All processing are done)

> If the return value is false or exception throws in preHandle(), postHandle() and afterCompletion() both are not invoked
