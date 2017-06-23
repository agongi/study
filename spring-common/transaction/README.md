## Transaction

```
ㅁ Author: suktae.choi
ㅁ Date: 2017.06.23
ㅁ References:
  - https://www.credera.com/blog/technology-insights/open-source-technology-insights/aspect-oriented-programming-in-spring-boot-part-2-spring-jdk-proxies-vs-cglib-vs-aspectj/
  - https://www.mkyong.com/spring3/spring-aop-aspectj-annotation-example/
  - http://www.mkyong.com/spring/spring-aop-examples-advice/
  - http://haviyj.tistory.com/33
```

### Transaction



Transaction에서 익셉션 발생 시 기본적으로 롤백되는 것과 아닌 것이 있는데 무엇인가? http://lng1982.tistory.com/128
- default로 unchecked exception 인 RuntimeException 만 롤백
- checked 까지 롤백 하려면, <tx:method name="insert*" propagation="REQUIRED" rollback-for="Exception" />

Transaction에 여러 작업들 중 익셉션 발생 시 일부 작업은 커밋되도록 하는 방법은?

Spring AOP Proxy <tx:annotation-driven proxy-target-class="true" /> 이 설정으로 방식 선택가능
