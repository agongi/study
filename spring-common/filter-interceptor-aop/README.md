## Filter vs Interceptor vs AOP
Simple comparison between filter and interceptor.

>###### Filter and Interceptor work almost similar, but invoked time is different.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.17 ~
ㅁ References:
 - http://changpd.blogspot.kr/2013/03/spring.html
 - http://dev-eido.tistory.com/entry/Interceptor-filter-AOP%EC%9D%98-%EC%B0%A8%EC%9D%B4
```

<img src="https://github.com/agongi/study/blob/master/spring-common/filter-interceptor-aop/images/89101625_26c5be9fd9.jpg" width="75%">

#### 1. Filter
It is invoked in servlet container(web.xml) `before dispatcher servlet`.

```xml
<filter>
    <filter-name>FILTER_NAME</filter-name>
    <filter-class>com.sec.filter.UserFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>FILTER_NAME</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

#### 2. Interceptor
It is invoked `after dispatcher servlet`.

```xml
<mvc:interceptors>
   <mvc:interceptor>
      <mvc:mapping path="/api1/*" />  
      <mvc:mapping path="/api2/*" />  
      <mvc:mapping path="/api3/*" />  
      <bean class="com.sec.interceptor.UserInterceptor" />
    </mvc:interceptor>
</mvc:interceptors>
```

You can control request in below :
 - preHandle() : before @Controller
 - postHanle() : After @Controller, Before view
 - afterCompletion() : After view

#### 3. AOP
PointCut 대상에게, Aspect 를 Advice 한다.
- Pointcut
  - @Pointcut("execution(public * com.abc.service.*.*())")
- Aspect
  - @Aspect public class ABC {...}
- Advice
  - before
  - after
  - around
