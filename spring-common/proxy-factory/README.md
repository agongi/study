## Proxy Factory

```
ㅁ Author: suktae.choi
ㅁ References:
- https://blog.outsider.ne.kr/851
```

Applicable of proxying bean programmatically

```java
ProxyFactory factory = new ProxyFactory(myBusinessInterfaceImpl);
factory.addAdvice(myMethodInterceptor);
factory.addAdvisor(myAdvisor);

MyBusinessInterface tb = (MyBusinessInterface) factory.getProxy();
```
