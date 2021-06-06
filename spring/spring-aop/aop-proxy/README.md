## AOP Proxy

```
ㅁ Author: suktae.choi
ㅁ References:
- https://gmoon92.github.io/spring/aop/2019/04/20/jdk-dynamic-proxy-and-cglib.html
- https://www.baeldung.com/cglib
- https://www.baeldung.com/java-dynamic-proxies
```

### 구현체 종류

**JDK dynamic proxy**

JDK proxying uses the java.reflection.proxy

- runtime weaving
- applied in interface

```java
// handler
public class UserServiceInvocationHandler implements InvocationHandler {
  @Override
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    // do something
    if (method.getName().equals("getName")) {
      return "proxied!";
    } else {
      throw new UnsupportedOperationException();
    }
  }
}

// create proxy
Object proxy = Proxy.newProxyInstance(
  ClassLoader.getSystemClassLoader(), 
  new Class[] { UserService.class }, 
  new UserServiceInvocationHandler());

// use
String name = (String)((UserService)proxy).getName();
```

**CGLIB**

CGLIB proxying works by generating a subclass of the target class at runtime

- runtime weaving
- applied in target-class
- bytecode instrument

```java
// create proxy
Enhancer enhancer = new Enhancer();
enhancer.setSuperclass(UserService.class);
enhancer.setCallback((FixedValue) () -> "foo, bar");
UserService proxy = (UserService) enhancer.create();

// use
String res = proxy.sayHello("anyValue");
assertEquals("foo, bar", res);
```

> spring 4.0 부터 AOP 기본구현체가 JDK -> CGLib 로 변경됨 - https://github.com/spring-projects/spring-boot/issues/8434

**AspectJ**

AspectJ proxying works by instruments code snippet in target class directly in compile time

- compile weaving
- bytecode instrument

