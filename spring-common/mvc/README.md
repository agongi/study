## MVC

```
ㅁ Author: suktae.choi
ㅁ References:
```

#### Index

- 

### Cores

InternalResourceViewResolver

```java
@Bean
public InternalResourceViewResolver internalResourceViewResolver() {
  InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
  viewResolver.setPrefix("/WEB-INF/jsp/");
  viewResolver.setSuffix(".jsp");
  
  return viewResolver;
}
```

