## Spring AOP

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.07.20
ㅁ References:
 - https://blog.outsider.ne.kr/850
 - http://changpd.blogspot.kr/2013/03/spring.html
 - http://dev-eido.tistory.com/entry/Interceptor-filter-AOP%EC%9D%98-%EC%B0%A8%EC%9D%B4
```

### Nutshell
- Pointcut
  - @Pointcut("execution(public * com.abc.service.*.*())")
- Aspect
  - @Aspect public class ABC {...}
- Advice
  - before
  - after
  - around
