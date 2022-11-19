## Closure

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.12.05
ㅁ References:
 - http://d2.naver.com/helloworld/4911107
```

### Terms
#### Closure
A function that can access external variables enclosing itself.

A variable that is accessible from closure called **free variable**.

```java
private final List<String> friends = Arrays.asList("aaa", "bbb", "ccc", "ddd");

@Test
public void test00_closure() {
    String value = "hello ";  // free variable

    friends.forEach(friend -> { // It is a closure have references out of lambda scope
        log.info(value + friend); // free variable will be treated final implicitly or should be declared final explicitly in definition
    });
}
```

#### Lambda
A Interface or abstract class that has **only one abstract method** can be used in lambda expression.

They can be declared `@FunctionalInterface` annotation. All collections are the good members of lambda.
