## BeanFactory vs ApplicatioContext

```
ㅁ Author: suktae.choi
ㅁ References:
- http://javarevisited.blogspot.kr/2012/11/difference-between-beanfactory-vs-applicationcontext-spring-framework.html
- https://stackoverflow.com/questions/243385/beanfactory-vs-applicationcontext
```

**Bean Factory**

- Bean instantiation/wiring (IoC)
  - lazy initialization

**Application Context**

- Bean instantiation/wiring (IoC)
  - eager initialization
- Automatic BeanPostProcessor registration
- Automatic BeanFactoryPostProcessor registration
- Access to messages in i18n-style, through the MessageSource interface.
- Access to resources, such as URLs and files, through the ResourceLoader interface.

> ApplicationContext inherits BeanFactory so It supports all features provided in BeanFactory.