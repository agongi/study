## Multi Module Project
Most of java based project is flat layout but sometimes you need to configure it hierarchically for avoiding duplicated logic, management etc,.

>###### Multi module project is consisted of root, module and common projects.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.05.25
ㅁ References:
 - http://blog.naver.com/PostView.nhn?blogId=choigohot&logNo=40188252806
```

Root project have 3 children, module-1, module-2 and module-common. module-1 and module-2 projects are referencing module-common as a local project library.

```
            Modules

Module-1  Module-2  Module-common
    |         |           ^
    |         |           |
    ㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡㅡ  
```

#### Overview

<img src="https://github.com/agongi/study/blob/master/maven-gradle/multi-module-project/images/Screen%20Shot%202016-06-08%20at%2002.27.47.png" width="50%">

#### 1. Root Project (multiModules)

 - configure packaging type as `pom`
 - create `modules` schema and add each of child projects in `module`
 - remove src folder in project

 > It doesn't need any of java files, It is only set up for children projects.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sec</groupId>
    <artifactId>multiModules</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>multiModules-common</module>
        <module>multiModules-module1</module>
        <module>multiModules-module2</module>
    </modules>
</project>
```

#### 2. Module Project (multiModules-module1/multiModules-module2)

 - create `parent` schema and add root project in it
 - add common-project for referencing in `dependency` schema used to normal maven dependencies

```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <project>
     <parent>
         <artifactId>multiModules</artifactId>
         <groupId>com.sec</groupId>
         <version>1.0-SNAPSHOT</version>
     </parent>

     <modelVersion>4.0.0</modelVersion>

     <groupId>com.sec</groupId>
     <artifactId>multiModules-module1</artifactId>
     <version>1.0-SNAPSHOT</version>

     <dependencies>
         <dependency>
             <groupId>com.sec</groupId>
             <artifactId>multiModules-common</artifactId>
             <version>1.0.0</version>
         </dependency>
     </dependencies>
 </project>
```

#### 3. Common Project (multiModules-common)

 - define proper `groupId`, `artifactId` and `version` to be referenced

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <parent>
        <artifactId>multiModules</artifactId>
        <groupId>com.sec</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sec</groupId>
    <artifactId>multiModules-common</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
</project>
```
