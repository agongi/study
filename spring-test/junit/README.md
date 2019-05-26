## JUnit

```
ㅁ Author: suktae.choi
```

### Intro
#### @RunWith
When a class is annotated with @RunWith or extends a class annotated with @RunWith, JUnit will invoke the class it references to run the tests in that class instead of the runner built into JUnit.
> @RunWith(SpringJUnit4ClassRunner.class)
>
> spring applicationContext 가 필요하면 SpringJUnit4ClassRunner 지정

#### @ExtendWith

This enables spring-core features also with spring-boot's

> @ExtendWith(SpringExtension.class)

