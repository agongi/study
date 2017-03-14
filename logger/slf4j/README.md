## SLF4J

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.03.13
ㅁ References:
 - https://lalwr.blogspot.kr/2016/03/logback.html
```

<img src="https://github.com/agongi/study/blob/master/logger/slf4j/images/bindings.png" width="75%">

### Concept
SLF4J is a `Logging facade` as an interface for various logging frameworks (e.g. logback, log4j) allowing the end user to plug in the desired logging framework at deployment time.

### Migration
If your application uses 3rd-party libraries in dependency, It is hard to control to force them to use desired logging framework you choose. SLF4J allows you easily override existing logging-legacy `xxx-over-slf4j` xxx stands for legacy logging system such as log4j, apache common loggings.

The concept is very simple. `xxx-over-slf4j` series uses the same package name what legacy logging used, it makes 3rd-party's log flow to SLF4J.
