## -Aware

```
ㅁ Author: suktae.choi
ㅁ References:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    
```

`Aware` interface is used to modify something in spring-container's functionalities in bootstrap-phase.

| Interface                      | Resources                                                    |
| ------------------------------ | ------------------------------------------------------------ |
| BeanNameAware                  | intercept beanName before creation                           |
| BeanFactoryAware               | DI                                                           |
| ApplicationContextAware        | DI                                                           |
| EnvironmentAware               | DI                                                           |
| ApplicationEventPublisherAware | @TransactionalEventListener being invoked when this#publishEvent |
| ResourceLoaderAware            | DI                                                           |
| MessageSourceAware             | DI                                                           |

If you implements interface, this code is partial of spring-framework (strong-interest), then you code is coupled with spring's one.

> Just need to use it? then @Autowire it not using aware- interfaces.