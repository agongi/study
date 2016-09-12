## 톰캣 #06-07 JDBC 및 Host
#### JDBC


>###### JDBC 개념 vs ORM 비교

mybatis / hibernate등의 ORM(Object-Relational-Mapping)으로 많이 대체되었지만, Tomcat에서도 기본적인 JDBC를 지원한다.

다음 예제를 살펴보자 (context.xml)

![img-context.xml](https://github.com/agongi/study/blob/master/tomcat/%2306-07/images/Screen%20Shot%202015-07-09%20at%202.07.43%20AM.png)

context.xml에서 기본적인 DB쿼리는 가능하지만, 되도록이면 ORM (Mybatis / Hibernate)를 사용하는 것을 추천한다. 다음 예제에서 ORM vs JDBC를 정리한 글을 확인해보자

[ORM vs JDBC](http://www.java-samples.com/showtutorial.php?tutorialid=813)

#### Multiple service 운영
>###### 1. Virtual Host

![img-virtualhost](https://github.com/agongi/study/blob/master/tomcat/%2306-07/images/Screen%20Shot%202015-07-17%20at%203.54.22%20AM.png)

PRD에서 이런식으로 운영할 경우는 거의 없겠지만(하지말자), 1개의 Tomcat에서 N개의 도메인을 호스팅 할때, Virtual Host를 사용한다.

```xml
<Engine name="Catalina" defaultHost="localhost">
  <Host name="mail.samsung.net" appBase="webapps1" unpackWARs="true" autoDeploy="true">
    ...
  </Host>
  <Host name="blog.samsung.net" appBase="webapps2" unpackWARs="true" autoDeploy="true">
    ...
  </Host>

</Engine>
```
 - appBase: {CATALINA_HOME}/ 에서의 상대경로(relative path)로 설정한다. ex.) /var/lib/tomcat7/webapps
 - name: matching될 Domain을 입력한다.

Tomcat이 HTTP Request를 받으면, Header -> Host 와 동일한 <Host name="">을 찾은 후, 지정된 appBase로 바인딩 한다.

![img-virtualhost](https://github.com/agongi/study/blob/master/tomcat/%2306-07/images/Screen%20Shot%202015-07-17%20at%203.33.52%20AM.png)


>###### 2. Port

동일 IP(Domain)에서 다른 port를 할당해, 다수의 서비스를 운영하는 방법도 있다. server.xml에서 <Service> Tag를 추가하면된다.

대신 기존 설정에서 사용중인 port를 동일하게 사용할 순 없으므로, 다른 port를 설정한다.

![img-port](https://github.com/agongi/study/blob/master/tomcat/%2306-07/images/Screen%20Shot%202015-07-17%20at%203.52.22%20AM.png)


>###### 3. Context

톰켓이 기본적으로 보고 있는 루트 컨텍스트는 webapps/ROOT 입니다.
```xml
<Host name="localhost"  appBase="webapps"
      unpackWARs="true" autoDeploy="true"
      xmlValidation="false" xmlNamespaceAware="false">
</Host>
```
appBase는 ${catalina-home} 밑의 상대경로를 인자로 받으며 ```<Context>``` 태그가 생략되어 있으면 기본적인 루트는 ROOT 디렉토리 밑이 된다.

따라서, 톰켓이 설치가 되면 기본적으로 보고 있는 루트 컨텍스트는 ${catalina-home}/webapps/ROOT 이다.

server.xml을 수정할때, 우리가 주의깊게 봐야 할 부분은 다음과 같다.

 - **URL** ==> {DOMAIN} + path
 - **WEB_HOME** ==> appBase + docBase;

```xml
<Context>에서 path설정시 topic 없이 설정하고 싶다면

 [X] <Context path="/" />
 [O] <Context path="" />

이므로, 헷갈리지 조심하자
```

>###### 1. webapp 자체를 웹루트 디렉토리로 만들고 싶을 때,

```xml
<Host name="localhost"  appBase="webapps"
      unpackWARs="true" autoDeploy="true" >
<Context path="" docBase="." reloadable="true"/>
</Host>
```

>###### 2. webapp/test/ROOT를 웹루트 디렉토리로 만들고 싶을 때,

```xml
<Host name="localhost"  appBase="webapps/test"
      unpackWARs="true" autoDeploy="true"
      xmlValidation="false" xmlNamespaceAware="false">
</Host>
```

>###### 3. /home/test/webhome/my 를 웹루트 디렉토리로 만들고 싶을 때,

```xml
<Host name="localhost"  appBase="/home/test/webhome/my"
      unpackWARs="true" autoDeploy="true"
      xmlValidation="false" xmlNamespaceAware="false">
<Context path="" docBase="." reloadable="true"/>
</Host>

or

<Host name="localhost"  appBase="/home/test/webhome"
      unpackWARs="true" autoDeploy="true"
      xmlValidation="false" xmlNamespaceAware="false">
<Context path="" docBase="my" reloadable="true"/>
</Host>
```
