## Spring Security

```
@author: suktae.choi
```

Spring Security is easily enabled using providing `AbstractSecurityWebApplicationInitializer`:

```java
public class BaseSecurityWebApplicationInitializer extends AbstractSecurityWebApplicationInitializer {
  
  public BaseSecurityWebApplicationInitializer() {
    super(
      AppBaseConfig.class,
      BaseSecurityConfig.class);
  }
}
```

```java
@Configuration
@EnableWebSecurity
public class BaseSecurityConfig extends WebSecurityConfigurerAdapter {
	// ..
}
```

### Add Matched URL

```java
@Configuration
@EnableWebSecurity
public class BaseSecurityConfig extends WebSecurityConfigurerAdapter {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.http.authorizeRequests()
	      .anyMatchers("/admin/**").hasAuthority("USER")	// requires USER
  	    .anyMatchers("/api/**").permitAll()	// permit ALL
    	  .anyRequest().authenticated()	// others needs authentication
      	.and()
      .csrf().disable();	// disable it if has alternative token-based flow
  }
```

### Logout

```java
@Configuration
@EnableWebSecurity
public class BaseSecurityConfig extends WebSecurityConfigurerAdapter {
  @Bean
  public LogoutHandler customLogoutHandler() {
    return (req, res, auth) -> {
      Cookie[] cookies = req.getCookies();
      // .. logout things
    };
  }

  @Override
  protected void configure(HttpSecurity http) throws Exception {    				 
    http.logout().addLogoutHandler(customLogoutHandler())
  }
}
```

### Browser Page Cache Disable

Browser caches previous-page even if you already have logout.

> That page may show information only for login user.

- Disable default headers that is injected automatically
- Add no-cache header in response

```java
http.headers().defaultsDisabled().cacheControl()
  .and() // ...
```

### Extras

#### Method Call ACL (/w Entity modification)

```java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class BaseSecurityConfig extends WebSecurityConfigurerAdapter {
	// ..
}
```

- @Secured
- @PreAuthorized: before method call
- @PostAuthorized: after method call

```java
public class SecureMethod {
	@Secured({"ADMIN", "MANAGER"})
  public void remove(Long id) {
    // ..
  }
  
  @PreAuthorize("hasAuthority('ADMIN')")
  public void addPerson(Person person) {
    // ..
  }
  
  @PostAuthorize("returnObject.owner == authentication.name")
  public Person findById(Long id) {
    return personRepository.findOne(id);
  }
}
```

#### WebFlux ACL

```java
@Configuration
@EnableWebFluxSecurity
public class BaseSecurityConfig extends WebSecurityConfigurerAdapter {
	// ..
}
```

