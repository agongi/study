## 톰캣 #01-03 소개 및 설치

#### 정의
AP Server ( Application Server ) 의 한 종류이다.

JSP/Servlet engine ( == Web Container ) 으로 정의할 수 있다.

톰캣과 비슷한 종류의 WAS의 점유율은 다음과 같다.

![image-alt](https://github.com/agongi/study/blob/master/tomcat/%2301-03/images/Screen%20Shot%202015-07-01%20at%201.57.08%20AM.png)

#### 구성
 - Coyote [ Servlet Container ]: Java Servlet을 호스팅
 - Catalina [ HTTP Component ]: Protocol지원
 - Jasper [ JSP Engine ]: JSP를 처리

![image-alt](https://github.com/agongi/study/blob/master/tomcat/%2301-03/images/Screen%20Shot%202015-07-01%20at%202.08.54%20AM.png)

#### WAS란?
JavaEE Spec을 만족하는 Web Container를 의미한다.

Tomcat은 light-weight을 목표로 필수적인 EE Spec을 제외하곤, 대부분 지원하지 않는다. **즉 엄밀히는 Tomcat은 WAS가 아니다.**

EE full-spec을 지원하는 것으로는, TomEE가 있다.

국내에는 Spring + Tomcat 조합이 전자정부 프레임워크로 인해 사용률이 높다. Tomcat이 WAS가 아닌데도 웹서버가 개발/운영이 되는 이유는, Spring DispatcherServlet에서 Tomcat에서 지원하지 않는, EE Spec을 지원하기 때문이다.

Tomcat이 관리하는 web.xml을 보면 다음과 같이 설정되어 있다.
```xml
<servlet>
	<servlet-name>Spring MVC Dispatcher Servlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/web-application-config.xml</param-value>
	</init-param>
</servlet>
<servlet-mapping>
	<servlet-name>Spring MVC Dispatcher Servlet</servlet-name>
	<url-pattern>/spring/*</url-pattern>
</servlet-mapping>
```
HTTP request -> Tomcat web.xml -> /spring/* 패턴이면 -> org.springframework.web.servlet.DispatcherServlet 의 흐름으로 진행된다.

#### 버전 고려사항
![image-alt](https://github.com/agongi/study/blob/master/tomcat/%2301-03/images/Screen%20Shot%202015-07-02%20at%203.10.31%20AM.png)
 - JDK: Java Development Kit - Java를 이용한 개발시 필요한 Package들 까지 일괄 제공
 - JRE: Java Runtime Environment - Java로 개발된 실행파일을 구동시키기위한 환경

JDK를 설치하면 JRE도 포함되어 같이 설치된다. 즉 **JDK == JRE + Development Kit**

#### Native Library
Tomcat 5.5 이상 버전에서부턴, 실행시 다음 에러메세지가 출력된다.
```LOG
  정보: The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: ...
```
Native Library가 설치되지 않아, 나오는 경고 메세지 이다. 그렇다면 Native Library가 어떤 역할을 하는지 알아보자
![image-alt](https://github.com/agongi/study/blob/master/tomcat/%2301-03/images/Screen%20Shot%202015-07-03%20at%2012.11.11%20AM.png)
하지만 가장 큰 이유는 다음과 같다.
 - **Performance**

PRD Tomcat을 설정한다면, 반드시 Native Library도 설정해야 한다. local 개발환경에서 eclipse tomcat에 APR을 적용하려면 다음과 같이 진행한다.

 - ARP library download [apr-link](https://apr.apache.org/download.cgi)
 - paste library into following directory

 ```
{JAVA_INSTALLED_DIRECTORY}/JDK/JRE/bin
 ```
 - re-launch eclipse tomcat

 APR 경고메시지가 다음과 같이 변경되었는지 확인한다.

```LOG
INFO: Loaded APR based Apache Tomcat Native library 1.1.24 using APR version 1.5.2.
```
