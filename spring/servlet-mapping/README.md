## /* vs /

```
@author: suktae.choi
- http://lng1982.tistory.com/97
- https://okky.kr/article/145481
```

#### Spec
- A string beginning with a ‘/’ character and ending with a ‘/\*’ suffix is used for path mapping.
- A string beginning with a ‘\*.’ prefix is used as an extension mapping.
- A string containing only the ’/’ character indicates the "default" servlet of the application. In this case the servlet path is the request URI minus the context path and the path info is null.
- All other strings are used for exact matches only.

### /*
/\* 는 요청 받은 모든 URL을 의미한다. (아래와 같은 유형의 패턴 모두)

- /user
- /user/userList.jsp
- /img/user/user.png

```java
@RequestMapping(value = "/user/userList.jsp")
@RequestMapping(value = "/img/user/user.png")
```

가 존재하지 않으므로 404 NOT FOUND 가 발생한다.

### /
/ 는 url-pattern 에 걸리지 않는 나머지 케이스들을 의미한다.

web.xml 은
- {CATALINA_HOME}/conf/web.xml
- 각 서비스의 web.xml

에 정의된 url-pattern 을 확인하는데, \*.jsp 는 dispatcherServlet 을 타지 않도록 설정은 어떻게 할까?

#### {CATALINA_HOME}/conf/web.xml
```xml
<servlet>
    <servlet-name>default</servlet-name>
    <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
    <init-param>
        <param-name>debug</param-name>
        <param-value>0</param-value>
    </init-param>
    <init-param>
        <param-name>listings</param-name>
        <param-value>false</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet>
    <servlet-name>jsp</servlet-name>
    <servlet-class>org.apache.jasper.servlet.JspServlet</servlet-class>
    <init-param>
        <param-name>fork</param-name>
        <param-value>false</param-value>
    </init-param>
    <init-param>
        <param-name>xpoweredBy</param-name>
        <param-value>false</param-value>
    </init-param>
    <load-on-startup>3</load-on-startup>
</servlet>

<!-- The mapping for the default servlet -->
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
<!-- The mappings for the JSP servlet -->
<servlet-mapping>
    <servlet-name>jsp</servlet-name>
    <url-pattern>*.jsp</url-pattern>
    <url-pattern>*.jspx</url-pattern>
</servlet-mapping>
```

### Conclusion
/ 을 재정의하면 tomcat web.xml 의 설정은 무시된다.

<img src="https://github.com/agongi/study/blob/master/spring-common/servlet-mapping/images/Screen%20Shot%202017-06-19%20at%2002.01.16.png" width="75%">

- /img/user/user.png
  - WEB (apache, nginx) 에서 처리
- /user/userList.jsp
  - {CATALINA_HOME}/conf/web.xml 에 정의된 **\*.jsp** 에서 처리
- /user
  - **/** 으로 나머지 패턴에 대해서 모두 처리
