## JUnit

```
ㅁ Author: suktae.choi
ㅁ References:
```

### Index


### Intro
#### @RunWith
When a class is annotated with @RunWith or extends a class annotated with @RunWith, JUnit will invoke the class it references to run the tests in that class instead of the runner built into JUnit.
> 내장된 Runner 가 아닌 다른 Runner 로 테스트 수행, spring applicationContext 가 필요하면 SpringJUnit4ClassRunner 지정
