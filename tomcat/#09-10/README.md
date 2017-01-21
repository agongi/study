## 톰캣 #09-10 쓰레드 및 모니터링
#### 쓰레드


what is thread? 에 대한 질문은 답할수 있으리라 믿는다. 혹시 process vs thread의 개념이 clear하지 않다면, 다음 링크를 참조하자

[process-vs-thread](http://stackoverflow.com/questions/200469/what-is-the-difference-between-a-process-and-a-thread)

Tomcat에서는 CATALINA_HOME}/conf/server.xml의 connector 에서 Thread 관련 설정을 할 수 있다.

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />
<Connector port="8009" protocol ="AJP/1.3" redirectPort="8443" />
```

가 default settings이고, default에서는 Max Thread Value는 다음과 같다. (implicitly)

```
Default Max Thread Value = 200
```

각 Connector에 설정가능한 항목은 다음과 같다.

 - maxSpareThreads: Idle 상태로 유지할 max thread pool size
 - maxThreads: 동시요청시 Connector가 생성할수있는, 최대 request
 - minSpareThreads: tomcat 에서 유지할, Idle Thread
 - maxIdleTime: Thread 유지시간

각각의 설정을 명시적으로 추가한다면, 다음과 같다.

![img-thread-settings](https://github.com/agongi/study/blob/master/tomcat/%2309-10/images/Screen%20Shot%202015-07-22%20at%2012.42.06%20AM.png)

설정은 성공적으로 하였지만, 불필요하게 중복적용이 많은 듯 하다. 방법이 없을까? 물론 있다.

![img-executor](https://github.com/agongi/study/blob/master/tomcat/%2309-10/images/Screen%20Shot%202015-07-22%20at%2012.42.15%20AM.png)


#### 모니터링

>###### jkstatus
mod_jk를 통해 apache-tomcat을 연동하였다면, default설정으로 jkstatus가 binding 되어있다.
 - 설정: https://github.com/agongi/study/tree/master/tomcat/%2308

![img-jkstatus](https://github.com/agongi/study/blob/master/tomcat/%2309-10/images/Screen%20Shot%202015-07-22%20at%2012.29.08%20AM.png)

>###### visualVM
가장 보편적으로 사용하는 모니터링 도구이다. Tomcat뿐만아니라, JavaVM을 사용하는 모든 Application에 대한 모니터링이 가능하다.

 - 설정: http://misoin.tistory.com/42

![img-visualVM](https://github.com/agongi/study/blob/master/tomcat/%2309-10/images/Screen%20Shot%202015-07-22%20at%2012.30.18%20AM.png)

>###### JMX
또다른 대안으로 JMX도 있다. 살펴보도록 하자
 - 설정: http://lesstif.com/pages/viewpage.action?pageId=20776824

![img-visualVM](https://github.com/agongi/study/blob/master/tomcat/%2309-10/images/Screen%20Shot%202015-07-22%20at%2012.45.11%20AM.png)

여러가지 Monitoring Tool을 살펴보았다. 그렇다면 뭘 써야하지? 결론부터 말하자면, **visualVM** 을 써라.

( JConsole uses only JMX, but VisualVM uses other monitoring technologies like Jvmstat, Attach API and SA in addition to JMX )
