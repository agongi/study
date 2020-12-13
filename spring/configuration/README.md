## @Configuration

```
ㅁ Author: suktae.choi
ㅁ References:
- http://haviyj.tistory.com/33
```

#### Index

- [@Import](import)
- [@DependsOn - smartlifecycle](dependsn-smartlifecycle)

***

#### @Configuration

It defines properties line XML-based settings

#### @Primary
If the same bean of type exists, @Primary one is used
> It will throw duplicate bean candidates blabla unless @Primary is annotated

#### @Lazy
Bean is created once getBean() is invoked

#### @DependsOn

bean initialization must run after that bean

#### @Import

add more @configuration

#### @ContextConfiguration

test packages. declare @configuration or any context

#### @Propertysources

with PropertySourcePlaceHolderConfigurer