## @PostConstruct
This section will describe why @PostConstruct is necessary to initialize a spring bean instead of regular constructor

> @PostConstruct has been performed after the bean is fully instantiated. Container guarantees @PostConstruct will be invoked only once.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.06.24 ~
ㅁ References:
 - http://stackoverflow.com/questions/3406555/why-use-postconstruct
```

- Constructor
 - The bean has not yet initialized
 - Maybe a bean is instantiated multiple times

- @PostConstruct
 - The bean has been initialized fully
 - Only once invoked in bean's lifecycle

```java
public class Foo {
    @Inject
    Logger LOG;

    @PostConstruct
    public void fooInit(){
        LOG.info("This will be printed; LOG has already been injected");
    }

    public Foo() {
        LOG.info("This will NOT be printed, LOG is still null");
        // NullPointerException will be thrown here
    }
}
```

> The injection of the dependencies has not yet occurred in a constructor.
