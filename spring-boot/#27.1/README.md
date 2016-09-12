#### 27.1. The Spring Web MVC framework
**Quick reference**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202015-12-04%20at%2002.13.11.png" width="50%">

**pom.xml**
```xml
<dependencies>
	<!-- default -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter</artifactId>
	</dependency>

	<!-- spring mvc -->
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>
```

**BootApplication.java**
```java
package com.sec;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class BootApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootApplication.class, args);
    }
}
```

**BootRestController.java**
```java
package com.sec.services.controller;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class BootRestController {
	private static final Logger logger = LogManager.getLogger();

    @RequestMapping("/")
    public String index() {
    	logger.info("logging spring boot");
      return "Greetings from Spring Boot!";
    }
}
```

**output**
```
suktaechoi@suwon-iMac-mac /Users/suktaechoi/Downloads $ curl http://localhost:8080/
Greetings from Spring Boot!
```

###### 27.1.1. Spring MVC auto-configuration
Use `@Configuration` with annotation `@EnableWebMvc` for default. (complete full-features of Spring MVC)

Use `@Configuration` without `@EnableWebMvc` for customize. Just implement `@Bean` of type `WebMvcConfiguraerAdpater` (interceptors, formatters, view controllers etc.) what you need.

###### 27.1.2. HttpMessageConverters
In charge of converting object to JSON or XML and reverse (we normally call serialization/deserialization).

Encoding is `UTF-8`, libraries are `jackson json` and `jackson XML` by default. you can customize it in this way.

```java
import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;

@Configuration
public class BootApplication {
    @Bean
    public HttpMessageConverters customConverters() {
        HttpMessageConverter<?> additional = ...
        HttpMessageConverter<?> another = ...
        return new HttpMessageConverters(additional, another);
    }
}
```

###### 27.1.3. MessageCodesResolver
Used generating error codes for rendering error messages from binding errors.

###### 27.1.4. Static Content
Default locations are following.
 - `classpath:/META-INF/resources/`
 - `classpath:/resources/`
 - `classpath:/static/`
 - `classpath:/public/`

You can also set up custom location using `spring.resources.static-locations`

```
 # SPRING RESOURCES HANDLING (ResourceProperties)
spring.resources.static-locations=/custom/public, /custom/public2
```

Special case `/webjars/**` is reserved in default locations for supporting [webjars](http://www.webjars.org/).
> Do not use src/main/webapp that is normally default in standard web application as known. It only works in .war packaing but .jar

Traditional cache-control is working only with HTTP Headers.
 - Expires
 - Cache-Control
 - Last-Modified

One of the solutions to get over the cache problem is [Cache busting](https://code.google.com/p/ant-web-tasks/wiki/CacheBusting).
 - client-side

 : append timestamp to the HTTP Request URL. e.g. `www.google.com?_=1346484617818`. Thereby client will misunderstand and not use stored cache because target url is always different.

 - server-side

 : versioned url in file content. e.g. `/js/react.min-7fb33fvwr.js`.<br> aggressive cache header e.g. 1 year

Enable Cache-busting such as `<link href="/css/spring-{HASH_CODE}.css"/>` in application.properties.

```
 # SPRING RESOURCES HANDLING (ResourceProperties)
spring.resources.chain.strategy.content.enabled=true # Enable the content Version Strategy.
spring.resources.chain.strategy.content.paths=/** # Comma-separated list of patterns to apply to the Version Strategy.
```
> resource links are rewritten at runtime in template, e.g. Thymeleaf and help from ResourceUrlEncodingFilter. Those are auto-configured.

Set up resources under "/js/lib/" fixed version rule `<link href="/v12/js/lib/mymodule.js"/>` while other resources will still use the `<link href="/css/spring-{HASH_CODE}.css"/>`.

```
 # SPRING RESOURCES HANDLING (ResourceProperties)
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
spring.resources.chain.strategy.fixed.enabled=true
spring.resources.chain.strategy.fixed.paths=/js/lib/
spring.resources.chain.strategy.fixed.version=v12
```

###### 27.1.5. ConfigurableWebBindingInitializer
WebBindingInitializer is in charge of mapping `http request parameters` to user defined `VO` (model).<br>
you can also customize it.

###### 27.1.6. Template engines
Spring Boot supports following template engines with auto-configuration.

 - [Thymeleaf](http://www.thymeleaf.org/)
 - [Velocity](http://velocity.apache.org/)
 - [FreeMarker](http://freemarker.incubator.apache.org/docs/)
 - Groovy
 - Mustache

Default template location is `/src/main/resources/templates` by default configuration.

> JSPs has known-limitations. (Jetty, Undertow doesn't support it) try to avoid it from using.

###### 27.1.7. Error Handling
Spring Boot provides an `/error` mapping to handle all errors by default.
> Default is `BasicErrorController` that implements ErrorController.

**Quick reference**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202016-01-29%20at%2000.23.12.png" width="50%">

**BootError.java**
```java
package com.sec.utils.error;

import org.springframework.boot.context.embedded.ConfigurableEmbeddedServletContainer;
import org.springframework.boot.context.embedded.EmbeddedServletContainerCustomizer;
import org.springframework.boot.context.embedded.ErrorPage;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;

@Component
public class BootError implements EmbeddedServletContainerCustomizer {

    @Override
    public void customize(ConfigurableEmbeddedServletContainer container) {        
        container.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
        container.addErrorPages(new ErrorPage(HttpStatus.NOT_FOUND, "/404"));
        container.addErrorPages(new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/500"));
    }
}
```

**For client,**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202016-01-28%20at%2022.05.12.png" width="50%">

**For browser,**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202016-01-28%20at%2022.05.20.png" width="50%">

- implements `ErrorController` interface for configuring more specific errors.
- extends `BasicErrorController` class for just adding new handler (add new in given mechanism).

**BootErrorController.java**
```java
package com.sec.services.controller;

import org.springframework.boot.autoconfigure.web.ErrorController;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class BootErrorController implements ErrorController {
    private static final String PATH = "/error";

    @RequestMapping(value=PATH)
    public String errorDefault() {
        return "xxx GLOBAL_EXCEPTION";
    }    
    @RequestMapping(value="/400")
    public String error400() {
        return "400 BAD_REQUEST";
    }
    @RequestMapping(value="/404")
    public String error404() {
        return "404 NOT_FOUND";
    }
    @RequestMapping(value="/500")
    public String error500() {
        return "500 INTERNAL_SERVER_ERROR";
    }

    @Override
    public String getErrorPath() {
        return PATH;
    }
}
```
**For 404 error, (defined)**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202016-01-29%20at%2000.29.54.png" width="50%">

**For 405 error, (undefined)**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202016-01-29%20at%2000.30.30.png" width="50%">

Traditional spring framework handles exception with `@ControllerAdvice` and `@ExceptionHandler` annotation.<br>
Here is a brief [example](http://www.mkyong.com/spring-mvc/spring-mvc-exceptionhandler-example/).

######  27.1.8. Spring HATEOAS
Hypermedia is an important aspect of REST. It allows you to build services that decouple client and server to a large extent and allow them to evolve independently. The representations `returned for REST resources contain not only data, but links to related resources`. Thus the design of the representations is crucial to the design of the overall service.

> You can see [quick guide](https://spring.io/guides/gs/rest-hateoas/).

######  27.1.9. CORS support
Cross-origin resource sharing (CORS) is that allows you to specify in a flexible way what kind of cross domain requests are authorized, instead of using some less secure and less powerful approaches like IFRAME or JSONP.

**BootRestController.java**
```java
package com.sec.services.controller;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class BootRestController {
	private static final Logger logger = LogManager.getLogger();

	  @CrossOrigin
    @RequestMapping(value="/", method=RequestMethod.GET)
    public String index() {
    	logger.info("logging spring boot");
        return "Greetings from Spring Boot!";
    }
}
```

> `@CrossOrigin` annotation could be also placed in @Controller for global applying.

It is also possible to make it default to all controllers.

**MyConfiguration.java**
```java
@Configuration
public class MyConfiguration {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurerAdapter() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**");
            }
        };
    }
}
```
**output**

<img src="https://github.com/agongi/study/blob/master/spring-boot/%2327.1/images/Screen%20Shot%202016-03-22%20at%2023.31.05.png" width="50%">
