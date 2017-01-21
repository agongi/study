## 톰캣 #04 환경설정
#### 환경설정


###### JVM 설정
eclipse를 사용해봤다면, eclipse.ini파일에 다음과 같은 설정을 봤을것이다.
```sh
-Xverify:none
-XX:+UseParallelGC
-XX:-UseConcMarkSweepGC
-XX:PermSize=64M
-XX:MaxPermSize=512M  
-XX:MaxNewSize=512M
-XX:NewSize=128M
-Xms1024m  
-Xmx1024m
```
tomcat과 eclipse는 JVM위에서 동작하는 application이므로 동일하게 설정이 가능하다. Tomcat의 설정에 대해 알아보자.
 - **JAVA_OPTS**: java application 모두 적용
 - **CATALINA_OPTS**: tomcat 에만 적용

```sh
JAVA_OPTS="%JAVA_OPTS%" -Xms128m -Xmx1024m -XX:MaxPermSize=256m -server
```


###### CLASSPATH 설정
classpath설정도 다른 JVM기반 앱과 다르지 않다. 다음 예제를 보자.
```sh
CLASSPATH="$CLASSPATH":/home/hosting_users/siksco7724/www/WEB-INF/lib/mysql-connector-java-5.1.6-bin.jar:
/home/{USER}/www/WEB-INF/lib/commons-dbcp-1.4.jar:
/home/{USER}/www/WEB-INF/lib/commons-pool-1.5.4.jar
```

환경변수를 적용할때, 다음 위치에 설정이 가능하다.
 - **bin/catalina.sh**: 기본 tomcat 설정
 - **bin/setenv.sh**: catalina.sh에 의해 호출됨. 개별설정 추가 가능

어느곳에 설정해도 동일하지만, tomcat guide에서는 사용자 설정은 setenv.sh에 하도록 권장된다. catalina.sh은 환경변수 뿐만 아니라, 기타 톰캣의 모든 설정이 지정되기 때문에, 수정시 복잡도가 증가한다. 유지/보수 등을 고려하면 catalina.sh는 default로 두고, setenv.sh에 하는것이 맞다. tomcat 최초 실행시에는, setenv.sh가 없다. 직접 다음 커맨드를 통해 생성해야 한다
```bash
touch {CATALINA_HOME}/bin/setenv.sh
```


#### WAS설정
tomcat이 start 후, servlet/jsp, encoding, session 등 WAS설정이 필요하다. 차례대로 알아보자
 - **{CATALINA_HOME}/conf/web.xml**: tomcat에서 구동되는 모든 web instance에 일괄적용
 - **WEB-INF/web.xml**: 각 service별 별도 설정 가능
모든 서비스에 일괄 적용할 설정은 ( ex. encoding=UTF-8 ) {CATALINA_HOME}/conf/web.xml에 설정하고, 각 서비스별 설정은 WEB-INF/web.xml에 설정하면된다. 동일한 설정이 중복된 경우, 톰캣은 WEB-INF/web.xml의 설정으로 override한다.

tomcat guide를 보면, 시스템별 default설정은 /conf/web.xml에 설정 후, 각 서비스별 설정을 WEB-INF/web.xml하도록 권장한다.
ex.) tomcat listen port등의 전체설정은, {CATALINA_HOME}/conf/web.xml에 한다.
![img-server.xml](https://github.com/agongi/study/blob/master/tomcat/%2304/images/Screen%20Shot%202015-07-06%20at%2012.06.21%20AM.png)


#### Log
tomcat구동시 /log에 기본적으로 다음의 로그가 생성된다.
![img-log](https://github.com/agongi/study/blob/master/tomcat/%2304/images/Screen%20Shot%202015-07-05%20at%2011.50.51%20PM.png)

이중 개발자가 log4j or console log를 통해 출력되는 파일은, **catalina.out** 이다. 그리고 기본으로 dailyRolling이 설정되어 일별로 catalina.out파일을 따로 저장한다.


#### Standard Directory Layout
![img-log](https://github.com/agongi/study/blob/master/tomcat/%2304/images/Screen%20Shot%202015-08-19%20at%201.41.58%20AM.png)
