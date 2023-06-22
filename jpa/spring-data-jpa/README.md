# Spring Data JPA

```
@author: suktae.choi
```

@QueryHint

@Lock

@Query
@Modifying

[@PersistenceContext 를 사용해야 하는 이유](https://batory.tistory.com/497)
- proxy 를 가져온다
- 실제로 사용이되는 시점에 thread 단위에서 가져오거나 생성된 em 으로 실행한다

https://www.4te.co.kr/892 의 의미를 좀더파악해야함
https://colevelup.tistory.com/21 하고같이
https://jiwondev.tistory.com/255