## Executable Jar

```
ㅁ Author: suktae.choi
- https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-executable-jar-format.html
- https://java.ihoney.pe.kr/523
- https://www.baeldung.com/gradle-fat-jar
- https://meteorkor.tistory.com/90
```

#### Blog

- [(java -cp) java -jar 실행시 외부 jar 파일에 대한 classpath 가 동작하지 않는다.](https://minchangdev.wordpress.com/2014/04/16/)
- [(java -uf) jar 파일 특정 파일만 교체, 바꾸는 방법](https://jun7222.tistory.com/583)

***

Fat Jar is a **executable jar that contains all dependencies** so It can run standalone.

## Prerequisites

- Gradle Tasks

```groovy
$ ./gradlew build --scan
> Task :compileJava
> Task :processResources NO-SOURCE
> Task :classes
> Task :jar
> Task :assemble
> Task :compileTestJava
> Task :processTestResources NO-SOURCE
> Task :testClasses
> Task :test
> Task :check
> Task :build
```

기본적으로 제공되는 clean, run, build 는 각각 정의된 task 를 실행하는 CommandSet 이다.

apply plugin: 'java' 으로 플러그인을 사용하면, custom task & override default 등을 통해 원하는 작업을 수행한다.

- Default Tasks

```groovy
defaultTasks 'clean', 'run'
```

`./gradlew` is equivalent to `./gradlew clean run`.

- Task Dependencies

```groovy
task hello {
  doLast {
    println 'Hello world!'
  }
}

task intro {
  // task:hello is executed before intro
  dependsOn hello
  
  doLast {
    println "I'm Gradle"
  }
}

$ gradle -q intro
Hello world!
I'm Gradle
```

## Spring Boot

### [Gradle Plugins](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/html/)

```groovy
plugins {
	id 'org.springframework.boot' version '2.2.6.RELEASE'
}

apply plugin: 'java'
apply plugin: 'io.spring.dependency-management'
```

- java: build.gradle 에서 `bootJar`, `bootWar` 태스크를 선언한다.
  - bootJar 와 bootWar 은 jar, war task 를 기본적으로 비활성화 처리하므로, 의존모듈은 아래와 같이 처리필요합니다:

```groovy
bootJar {
  enabled = false
}

jar {
  enabled = true 
}
```

설정후 아래의 명령어로 boot-app-bootableJar 를 생성합니다.

```bash
$ ./gradlew {project}:bootJar
```

빌드 결과물은 {PROJECT_HOME}/build/libs 에 생성됩니다.

구조를 살펴보기위해 jar 압축을 풀어봅니다:

```bash
$ jar xvf example.jar;tree
example.jar
 |
 +-META-INF
 |  +-MANIFEST.MF
 +-org
 |  +-springframework
 |     +-boot
 |        +-loader
 |           +-<spring boot loader classes>
 +-BOOT-INF
    +-classes
    |  +-mycompany
    |     +-project
    |        +-YourClasses.class
    +-lib
       +-dependency1.jar
       +-dependency2.jar
```

## Layout

```
Manifest-Version: 1.0
Main-Class: org.springframework.boot.loader.JarLauncher
Start-Class: com.naver.test.TestApplication
Spring-Boot-Version: 2.2.5.RELEASE
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
```

### /META-INF/MANIFEST.MF

java -jar 을 통해 프로그램을 실행하면, program main 의 위치를 알려줘야한다. jar 스펙은 MANIFEST.MF 에 정의된 값을 사용하거나 실행시점에 전달된 argunemt 를 사용한다.

- Manifest-Version: 필수값. 버전을 명시한다.
- **Main-Class**: 필수값. 해당 클래스의 `public static void main()` 을 찾아서 실행한다.

### /BOOT-INF, /WEB-INF

BootJar 의 java spec 은 META-INF 까지만 정의되어 있다. boot-plugin 에서 생성하는 속성들은 아래와 같다:

- /BOOT-INF: 플러그인 추가정의. 프로그램을 실행하는 Main-Class 는 JarLauncher 에 위임하고, 작성한 프로그램은 `Start-Class` 에 지정해서 app 을 실행한다.
- Spring-Boot-Classes: app main
- Spring-Boot-Lib: app dependencies

### 실행

java -jar TEST.jar 파일을 실행하면 아래의 흐름으로 처리됩니다.

- (MANIFEST.MF) JAR 스펙에 따라 `Main-Class` 에 정의된 클래스의 main 실행
- (JarLauncher) main method 를 살펴보면

```java
protected void launch(String[] args) throws Exception {
  JarFile.registerUrlProtocolHandler();
  ClassLoader classLoader = createClassLoader(getClassPathArchives());

  launch(args, getMainClass(), classLoader);
}
```

- getClassPathArchives(): Spring-Boot-Classes, Spring-Boot-Lib 항목 resolve
- createClassLoader: Thread.currentThread().getContextClassLoader()
- getMainClass(): Start-Class 항목 resolve

즉 JAR 스펙에 따라 실행되는 `JarLauncher` 가 reflection 으로 `Start-Class 를 실행`하고, `Spring-Boot-Classes, Spring-Boot-Lib 에 정의된 path` 를 전달합니다.

> class/lib 은 원래 JAR root 에 flat 하게 있어야하지만, 하위 폴더에 존재하므로 해당 경로부터 루트로 잡도록 처리함

## Java

### ShadowJar

shadowJar plugin 을 사용하면 fat-jar 생성이 가능하다. 기존 jar task 를 override 해보자.

> jar task 는 기본적으로 작성한 코드만 패키징한다. 즉 의존성은 따로 runtime arguments 로 전달해야한다.

```groovy
apply plugin: 'java'
apply plugin: 'com.github.johnrengelman.shadow'

jar {
  manifest {
    // manifest
    attributes "Main-Class": "mycompany.project.Application"
  }

  from {
    // include dependencies -> fat-jar
    configurations.compile.collect { it.isDirectory() ? it : zipTree(it) }
  }
}
```

- manifest: MANIFEST.MF 에 기록한 attr 을 지정한다
- from: jar 에 들어갈  dependencies 지정

ShadowJar 은 아래의 케이스에도 사용가능하다.

- [Resolve Conflict](https://medium.com/@ruijiang/using-gradle-shadow-plugin-to-resolve-java-version-conflict-183bd6ea4228)

```groovy
apply plugin: 'java'
apply plugin: 'com.github.johnrengelman.shadow'

shadowJar {
  baseName = ''
  version = ''
  zip64 true

  // resolve 3rd-library conflicts
  relocate ('com.google.protobuf', 'some.other.package.protobuf')
  mergeServiceFiles()

  // .. omitted
}
```

- Add Missing in Runtime

```groovy
apply plugin: 'java'
apply plugin: 'com.github.johnrengelman.shadow'
// runtime dependency
shadow 'org.mycompany:some-module'

task copyShadows(type: Copy) {
  from configurations.shadow
  into 'build/libs'
}

// is equivalent to
task copyShadows() {
  copy {
    from configurations.shadow
    into 'build/libs'
  }
}

shadowJar {
  dependsOn copyShadows

  baseName = ''
  version = ''
  zip64 true

  // .. omitted
}
```

> java-security 는 패키징된 jar 안의 모듈을 참조하면 sandbox 정책에 의해 실행이 안되는 케이스가 있다. 그럴때는 java -cp './company.jar:Application.jar' 으로 runtime 에 전달해야하므로, copy 가 필요하다.

## 
