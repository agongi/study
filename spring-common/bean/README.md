## Spring Common

```
ㅁ Author: suktae.choi
ㅁ References:
```

#### Index
- [BeanFactory vs ApplicationContext](bean-factory-application-context)
- [FactoryBean](factory-bean)
- [BeanPostProcessor](bean-post-processor)

### Cores

#### Life-Cycle

##### Initialize

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

##### Destroy

- **@PreDestroy**
- **destroy** of `DisposableBean`
- A custom **destroy-method** definition