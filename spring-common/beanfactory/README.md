## BeanFactory

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.08.03
ㅁ References:
- http://duckranger.com/2012/04/spring-mvc-dispatcherservlet
- https://blog.outsider.ne.kr/902
- http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/beans/factory/BeanFactory.html
```

### Before Bean's Creation
- BeanFactoryPostProcessor's `postProcessBeanFactory`
  - All bean definitions will have been loaded, but no beans have been instantiated yet

### Get Started of Bean Creation
#### Initialize
- BeanNameAware's setBeanName
- BeanClassLoaderAware's setBeanClassLoader
- BeanFactoryAware's setBeanFactory
- EnvironmentAware's setEnvironment
- EmbeddedValueResolverAware's setEmbeddedValueResolver
- ResourceLoaderAware's setResourceLoader (only applicable when running in an application context)
- ApplicationEventPublisherAware's setApplicationEventPublisher (only applicable when running in an application context)
- MessageSourceAware's setMessageSource (only applicable when running in an application context)
- ApplicationContextAware's setApplicationContext (only applicable when running in an application context)
- ServletContextAware's setServletContext (only applicable when running in a web application context)
- **postProcessBeforeInitialization** of `BeanPostProcessors`
- **@PostConstruct**
- **afterPropertiesSet** of `InitializingBean`
- A custom **init-method** definition
- **postProcessAfterInitialization** of `BeanPostProcessors`

#### Destroy
-  **@PreDestroy**
-  **destroy** of `DisposableBean`
-  A custom **destroy-method** definition
