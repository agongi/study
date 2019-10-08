## AOP vs Interceptor

```
ㅁ Author: suktae.choi
ㅁ References:
- https://www.credera.com/blog/technology-insights/open-source-technology-insights/aspect-oriented-programming-in-spring-boot-part-2-spring-jdk-proxies-vs-cglib-vs-aspectj/
```

### AOP

@AfterThrowing/@After/@Around can handle it

### Interceptor

postHandle()/afterCompletion are not invoked when **exception thrown in target method**

