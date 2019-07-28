## Property Editor

```
ㅁ Author: suktae.choi
ㅁ Reference:
- https://www.baeldung.com/spring-mvc-custom-property-editor
- https://blog.outsider.ne.kr/825
```

Spring`s MVC can hook controller params before and/or after injected.

Here is brief example of controller-scope `@InitBinder` and Property Editor usage for **String <—> Object mapping.**

- @InitBinder

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

- PropertyEditor

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

