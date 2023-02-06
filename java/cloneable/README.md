## Java Cloneable
Java fundamental of Cloneable.

> Java Object has protected clone() method and it works fine once all member-fields are primitives. you need to explicitly define deep-copy mechanism if you need such as Collections or Class copy,.

```
@author: suktae.choi
- https://docs.oracle.com/javase/7/docs/api/java/lang/Cloneable.html
```

#### Application.java
```java
/**
 * @author suktae.choi
 */
@Slf4j
public class Application {

    public static void main(String[] args) {

        BaseCloneable baseCloneable1 = new BaseCloneable("aaa", "bbb");
        BaseCloneable baseCloneable2 = null;

        baseCloneable2 = (BaseCloneable) baseCloneable1.clone();

        log.debug(baseCloneable2.toString());
    }
}
```

#### BaseCloneable.java
```java
/**
 * @author suktae.choi
 */
@ToString
@AllArgsConstructor
@NoArgsConstructor
@Getter
public class BaseCloneable implements Cloneable {

    private String aaa;
    private String bbb;


    public Object clone() {

        BaseCloneable obj = new BaseCloneable();
        obj.aaa = this.aaa;
        obj.bbb = this.bbb;

        return obj;
    }
}
```

#### Output
```
2016-09-13 02:20:25 [main] DEBUG com.Application - BaseCloneable(aaa=aaa, bbb=bbb)
```
