## MVC

```
ㅁ Author: suktae.choi
ㅁ References:
```

#### Index

- [Binding & Validation](binding-validation)
- [MessageSource](message-source)

### Cores

#### ViewResolver

```java
@Bean
public InternalResourceViewResolver internalResourceViewResolver() {
  InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
  viewResolver.setPrefix("/WEB-INF/jsp/");
  viewResolver.setSuffix(".jsp");
  
  return viewResolver;
}
```

#### Interceptor

```java
public class AuthCheckInterceptor implements HandlerInterceptor {
  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    
    boolean authResult = /* auth check */ true;
    
    if (authResult) {
      return true;
    }

    return false;
  }

  @Override
  public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

  }

  @Override
  public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

  }
}
```

```java
@Configuration
public class BaseConfiguration implements WebMvcConfigurer {
  @Override
  public void addInterceptors(InterceptorRegistry registry) {
    registry.addInterceptor(authCheckInterceptor())
      .addPathPatterns("/")
      .excludePathPatterns("/static");
  }
  
  @Bean
  public AuthCheckInterceptor authCheckInterceptor() {
    return new AuthCheckInterceptor();
  }
}
```

#### Locale

#### ContentNegotiate

#### Exception

#### Excel/PDF