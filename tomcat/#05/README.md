## 톰캣 #05 Deploy
#### Serial deploy


>###### 1. ~~By manager~~

tomcat 설치시, manager enable되어 있다면, 웹 UI를 통해 배포를 쉽게 할 수 있다.

![img-manager](https://github.com/agongi/study/blob/master/tomcat/%2305/images/Screen%20Shot%202015-07-07%20at%201.03.59%20AM.png)

**보안상 취약하니, 사용하지 말자**


>###### 2. By webapps

server.xml에 기본적으로 다음과 같이 설정되어 있다.
```xml
<Host name="localhost" appBase="webapps" unpackWARs="true" autoDeploy="true">
```

 - name: 이름 설정
 - appBase: war 복사할때, deploy될 directory 위치 설정
 - unpackWARs: war에 대한 압축해제 유무 설정
 - autoDeploy: tomcat에서 자동으로 war 변경시, deploy할지 설정

![img-webapps](https://github.com/agongi/study/blob/master/tomcat/%2305/images/Screen%20Shot%202015-07-07%20at%2012.40.35%20AM.png)

**자동**: Context 설정이 필요하지 않다.

```xml
{CATALINA_HOME}/webapps/sample.war
{CATALINA_HOME}/webapps/ROOT.war

별도로 설정이 필요하지 않다. (묵시적 설정 - implicitly!)
<Context path="sample" docBase="{CATALINA_HOME}/webapps/sample" />
or
<Context path="" docBase="{CATALINA_HOME}/webapps/ROOT" />

사용자는 웹브라우져에서 해당 경로로 접근 가능하다.
http://localhost:8080/sample
or
http://localhost:8080/
```
특이한 점은, webapps/ROOT.war로 배포를 하면, 톰캣에서 path="ROOT"가 아닌 path="" 로 인식한다는 것이다.

**수동**: Context 설정이 필요하다.

```xml
/home/test/web/sample.war

별도로 설정이 필요하다. (명시적 설정 - explicitly!)
<Context path="" docBase="/home/test/web/sample" />
or
<Context path="sample" docBase="/home/test/web/sample" />

사용자는 웹브라우져에서 해당 경로로 접근 가능하다.
http://localhost:8080/
or
http://localhost:8080/sample
```

자동/수동의 차이는 다음과 같다.

**war파일의 위치**
  - **자동**: webapps/*
  - **수동**: 임의의 location

기술적으로, war파일의 위치를 제외하고 나머지는 동일하다.


>###### 3. By context.xml

기존에는 server.xml에 Context를 설정했지만, context는 변경이 자주 발생하므로 톰캣 5.5 이상 버젼에서는 context.xml로 따로 사용하도록 권고된다.

server.xml는 다음위치에 있는 \*.xml은 자동으로 loading한다.
![img-context.xml](https://github.com/agongi/study/blob/master/tomcat/%2305/images/Screen%20Shot%202015-07-08%20at%203.01.06%20AM.png)


다음 예제를 살펴보자

```xml
{CATALINA_HOME}/conf/ENGINE_NAME/HOSTNAME/hahaha.xml

hahaha.xml에 별도로 설정이 되어있다. (명시적 설정 - explicitly!)
<Context path="" docBase="/home/test/web/hahaha" />
or
<Context path="hahaha" docBase="/home/test/web/hahaha" />

사용자는 웹브라우져에서 해당 경로로 접근 가능하다.
http://localhost:8080/
or
http://localhost:8080/hahaha
```


#### Parallel deploy
기존의 방식은 순간적이지만, war 배포 시 tomcat의 재시작이 진행되고 그 순간에는 서비스 장애가 발생한다. 이번에 공부할 parallel deploy에서는 서비스순단 없이 배포를 가능하게 한다.

개략적인 개념은 다음과 같다.

![img-version](https://github.com/agongi/study/blob/master/tomcat/%2305/images/Screen%20Shot%202015-07-08%20at%204.04.52%20AM.png)

기술적으로 구현방법에 대해 알아보자. context.xml파일에 다음과 같이 설정을 한다.
 - docBase: sample##01, sample##02, sample##03 의 형식으로 버전을 구분 ( **##{버전}** )
 - path: 동일

![img-context.xml](https://github.com/agongi/study/blob/master/tomcat/%2305/images/Screen%20Shot%202015-07-08%20at%204.05.03%20AM.png)

다음과 같이 설정시, 동일 path로 2개의 서비스가 running될 수 있으며, 기존 세션은 ##01, 신규 세션은 ##02로 bind되어 배포시에도 기존 유저는 서비스 순단없이 이용이 가능하다.
