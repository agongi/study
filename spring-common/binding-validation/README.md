## Binding & Validation

```
ㅁ Author: suktae.choi
ㅁ Reference:
- https://www.baeldung.com/spring-mvc-custom-property-editor
- https://blog.outsider.ne.kr/825
- https://engkimbs.tistory.com/738
```

### Cores

#### Comparison

- PropertyEditor
  - scope: Controller
  - stateful
  - two-way binding
- Converter
  - scope: Application
  - stateless
  - one-way binding
- Formatter
  - scope: Application
  - stateless
  - two-way binding
    - Only works in String - Object bind

#### Index

`AnnotationMethodHandlerAdapter` 는 매칭된 URL 을 정의한 @Controller 를 호출하는 역할을 담당한다.

호출시 @RequestParam 이나 @ModelAttribute, @PathVariable 등 처럼 HTTP 요청을 파라미터 변수에 바인딩해주는 작업이 필요한 애노테이션을 만나면 WebDataBinder 에서 String (http req) -> Custom.class 로의 rule 이 정의된 `PropertyEditor` 를 검색후 바인딩을 한다.

따라서 custom object 를 method-binding 에 적용하려면

- PropertyEditor 정의
- WebDataBinder 에 등록

하는 과정이 필요하다.

### Controller-scoped

#### @InitBinder

Method level `@InitBinder` is used to register property editor that will hook request string to Object.

```java
@RestController
public class TestRestController {
  @InitBinder
  public void initBinder(WebDataBinder dataBinder) {
    dataBinder.registerCustomEditor(CustomType.class, new CustomTypeEditor());
  }  
  
  @RequestMapping(...)
  public void joinUser(@RequestParam CustomType type) {
    // ..
  }
}
```

> PropertyEditor affects the rules in the **same @Controller-scope** only.

#### PropertyEditor

Listener class for actual hooks of request string to controller.

```java
public class CustomTypeEditor extends PropertyEditorSupport {
  // req is binding to controller's
  @Override
  public void setAsText(String text) {
    String upper = CaseFormat.LOWER_CAMEL.to(CaseFormat.UPPER_UNDERSCORE, text);
    setValue(CustomType.valueOf(upper));
  }

  // just before 
  @Override
  public String getAsText() {
    return ((CustomType)getValue()).getDetailText();
  }
}
```

propertyEditor 는 동시성 환경에서 singleton-bean 으로 사용하면 안된다. request 환경에서 상태를 공유할수 있기때문에 항상 새로운 객체를 생성하거나, @Bean @Scope(value = "prototype") 으로 사용해야한다.

아래 코드를 봐보자:

```java
public class UserPropertyEditor extends PropertyEditorSupport {
  @Override
  public String getAsText() {
    return String.valueOf(((User)getValue()).intValue());
  }

  @Override
  public void setValue(Object value) {
    super.setValue(value);
  }
}
```

Object -> String 의 converting 시

- \#setValue 로 field set
- \#getAsText 로 converting 수행

\#setValue 를 하는시점에 stateful 하므로, 동시성 문제가 발생한다.

### Application-scoped

#### WebBindingInitializer

모든 Controller 에 공통으로 정의할 PropertyEditor 를 등록하려면, WebBindingInitializer 를 사용한다.

3.0 까지는 WebBindingInitializer 에 등록했는데, 3.1 로 올라가면서 `HandlerMethodArgumentResolver` 로 변경되었다.

```java
public class BaseWebConfig extends WebMvcConfigurerAdapter {
  @Override
  public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
    super.addArgumentResolvers(argumentResolvers);
    argumentResolvers.add(userMethodArgumentResolver);
  }
}
```

```java
@Component
public class UserMethodArgumentResolver implements HandlerMethodArgumentResolver {
  @Override
  public boolean supportsParameter(MethodParameter parameter) {
    return parameter.getParameterType() == User.class;
  }

  @Override
  public String resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
    String id = webRequest.getParameter("id");

    return new User(id);
  }
}
```

#### Converter

```java
public class BaseWebConfig extends WebMvcConfigurerAdapter {
  @Override
  protected void addFormatters(FormatterRegistry registry) {
    registry.addConverter(new UserConverter.StringToUser());
    registry.addConverter(new UserConverter.UserToString());
  }
}
```

```java
public class UserConverter {
  public static class StringToUser implements Converter<String, User> {
    @Override
    public Category convert(String source) {
      return new User(source);
    }
  }

  public static class UserToString implements Converter<User, String> {
    @Override
    public String convert(User source) {
      return source.getName();
    }
  }
}
```

#### Formatter

Converter + locale

```java
public class BaseWebConfig extends WebMvcConfigurerAdapter {
  @Override
  protected void addFormatters(FormatterRegistry registry) {
    registry.addConverter(new UserFormatter());
  }
}
```

```java
public class UserFormatter implements Formatter<User> {
  @Override
  public User parse(String text, Locale locale) throws ParseException {
    return new User(Integer.parseInt(text));
  }

  @Override
  public String print(User object, Locale locale) {
    return object.getId().toString();
  }
}
```

#### ConversionService

Converter & Formatters are registered in `ConversionService`.

#### MessageConverter

XML/JSON messages are se/deserialized using messageConverters.

Once added, HTTP body could be accepted in mentioned format using `Accept: application/json`

```java
public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
  converters.add(new StringHttpMessageConverter());
  converters.add(new MappingJackson2HttpMessageConverter());
}
```

### Annotations

#### @NumberFormat

#### @DateTimeFormat

#### @JsonFormat

```java
@JsonFormat(shape = JsonFormat.Shape.STRING, timezone = "Asia/Seoul")
private Date regDate;
```

### Validation

`BindingResult` is automatically created when the Controller params (ex. **RequestBody** or **ModelAttribute**) is binded.

```java
public void getUser(@RequestBody User user, BindingResult bindingResult) {
	new Validator().validate(user, bindingResult);
  
  if (bindingResult.hasErrors()) {
  	// .. handling
  }
}
```

or you can manually create it like this:

```java
public void getUser(@PathVariable String id) {
  User user = userRepository.findById(id);
	BindingResult bindingResult = new BeanPropertyBindingResult(user, "user");
  
	new Validator().validate(user, bindingResult);
  
  if (bindingResult.hasErrors()) {
  	// .. handling
  }
}
```

