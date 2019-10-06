## MVC

```
ㅁ Author: suktae.choi
ㅁ References:
```

#### Index

- [Binding](binding)
- [Validation](validation)
- [MessageSource](message-source)

### Cores

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
    // ...
  }

  @Override
  public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
    // ...
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

#### Locale

- AcceptHeaderLocaleResolver: configure locale based on accept-language
- SessionLocaleResolver
- CookieLocaleResolver

```java
/**
 * Cookie-based
 */
@Bean
public LocalResolver localeResolver() {
  CookieLocaleResolver localeResolver = new CookieLocaleResolver();
  localeResolver.setCookieName("LANG");
  localeResolver.setDefaultLocale(new Locale("en"));

  return localeResolver;  
}
```

#### ContentNegotiate

Defines supported mediaTypes to response

```java
public class BaseConfiguration extends WebMvcConfigurerAdapter {
  @Override
  public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    // supported mediaTypes
    Map<String, MediaType> mediaTypes = new HashMap<>();
    mediaTypes.put("json", MediaType.APPLICATION_JSON);
    mediaTypes.put("pdf", MediaType.valueOf("application/pdf"));
    mediaTypes.put("xls", MediaType.valueOf("application/vnd.ms-excel"));
		// configure
    configurer.mediaTypes(mediaTypes);
    configurer.defaultContentType(MediaType.APPLICATION_JSON);
  }
```

- URL 에 extension 이 있는 경우 (ex. GET /user.json)
  - mediaTypes Map 에 정의된 key 가 있는지 확인
  - 없으면 -> JAF (JavaBeans Activation Framework) 를 이용해서 판단
- URL 에 extension 이 없는 경우 (ex. GET /user)
  - `Accept` 헤더값으로 추론

#### MessageConverters

Configure Accepted requestBody types/

```java
public class BaseConfiguration extends WebMvcConfigurerAdapter {
  @Override
  public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
    converters.add(new StringHttpMessageConverter());
    converters.add(new MappingJackson2HttpMessageConverter(new ObjectMapper());
    converters.add(new ByteArrayHttpMessageConverter());
  }
}
```

#### PropertySourcesPlaceholder

```java
@Bean
public static PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer() {
  PropertySourcesPlaceholderConfigurer configurer = new PropertySourcesPlaceholderConfigurer();
  configurer.setFileEncoding(StandardCharsets.UTF_8.name());
  configurer.setIgnoreResourceNotFound(true);
  configurer.setIgnoreUnresolvablePlaceholders(true);

  return configurer;
}
```

#### Exception

Configurable exceptionHandler

```java
public class BaseConfiguration extends WebMvcConfigurerAdapter {
  @Override
  public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> exceptionResolvers) {
    exceptionResolvers.add(handlerExceptionResolver());
  }

  @Bean
  public HandlerExceptionResolver handlerExceptionResolver() {
    Properties properties = new Properties();
    properties.setProperty(DataNotFoundException.class.getSimpleName(), "dataNotFoundException");

    SimpleMappingExceptionResolver exceptionResolver = new SimpleMappingExceptionResolver();
    exceptionResolver.setDefaultErrorView("error");
    exceptionResolver.setExceptionMappings(properties);

    return exceptionResolver;
  }
}
```

But now annotation is supported like following:

```java
@ControllerAdvice
public class ExceptionHandlerAdvice {
  @ExceptionHandler(DataNotFoundException.class)
  public ResponseEntity<String> handleDataNotFound(DataNotFoundException e) {
    return ResponseEntity
      .status(HttpStatus.NOT_FOUND)
      .body("Page Not Found");
  }

  @ExceptionHandler
  public ResponseEntity<String> handle(Exception e) {
    return ResponseEntity
      .status(HttpStatus.INTERNAL_SERVER_ERROR)
      .body("Internal Server Error");
  }
}
```

#### Multipart

File uploading

```java
@Bean
public MultipartResolver multipartResolver() {
  CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();
  multipartResolver.setDefaultEncoding(StandardCharsets.UTF_8.name());
  return multipartResolver;
}
```

#### HandlerMethodArgumentResolver

Allows argument injected into controller from web requests.

```java
public class BaseConfiguration extends WebMvcConfigurerAdapter {
  @Override
  public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
    super.addArgumentResolvers(argumentResolvers);
    // allows injecting {@link Pageable} instances into controller methods
    PageableHandlerMethodArgumentResolver resolver = new PageableHandlerMethodArgumentResolver();
    resolver.setFallbackPageable(new PageRequest(0, 20));
    argumentResolvers.add(resolver);
  }
}
```

#### Excel/PDF

Excel is generated once contentNegotiation is supported in given web requests with header `Accept=application/vnd.ms-excel`.

```properties
compile "org.apache.poi.poi:x.y"
```

```java
public class ExcelXlsView extends AbstractXlsView {
  private static final String EXTENSION_NAME = ".xls";
  private static final String fileName = "test-file" + EXTENSION_NAME;

  @SuppressWarnings("unchecked")
  @Override
  protected void buildExcelDocument(
    Map<String, Object> model,
    Workbook workbook,
    HttpServletRequest request,
    HttpServletResponse response) throws Exception {

    Collection<Object> datas = (Collection<Object>)model.get("datas");
    
    Sheet sheet = workbook.createSheet();
    addHeaders(sheet);
    addRows(sheet, datas);

    setFileNameToResponse(response);
  }
	
  /**
   * https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Content-Disposition
   */
  private void setFileNameToResponse(HttpServletResponse response) {
    response.setHeader("Content-Disposition", "attachment; filename=\"" + fileName + "\"");
  }
  
  private void addHeaders(Sheet sheet) {
  	Row header = sheet.createRow(0);
    header.createCell((short)0).setCellValue("name");
    header.createCell((short)0).setCellValue("age");
  }
  
  private void addRows(Sheet sheet, Collection<Object> datas) {
  	datas.forEach(o -> {
      Row row = sheet.createRow(sheet.getLastRowNum() + 1);
	    row.createCell((short)0).setCellValue(o.getA());
  	  row.createCell((short)0).setCellValue(o.getB());
    });  
  }
}
```

You can even generate PDF using `AbstractPdfView`:

```properties
compile "com.lowagie.itext:x.y.z"
```

```java
public class ExcelXlsView extends AbstractPdfView {
  private static final String EXTENSION_NAME = ".pdf";
  private static final String fileName = "test-file" + EXTENSION_NAME;

  @SuppressWarnings("unchecked")
  @Override
  protected void buildPdfDocument(
    Map<String, Object> model,
    Document document,
    PdfWriter writer,
    HttpServletRequest request,
    HttpServletResponse response) throws Exception {

    Collection<Object> datas = (Collection<Object>)model.get("datas");
    
		// ... omitted
  }
}
```