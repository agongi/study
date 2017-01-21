## 톰캣 #11 팁
#### Tip!


>###### setenv.sh

뭔가 설정을 변경할 필요가 있다고 하자. 대체로 여러가지방법이 있을텐데, 다음을 살펴보자.
 - 최상: project의 설정/구현 으로 해결 (scheduling 등)
 - 중간: sub script 변경 (setenv.sh)
 - 최하: main script 변경 (catalina.sh)

서버의 경우 scale-out, migration등을 고려하면 war만 배포하면, 모든설정이 완료되도록 구현하는 것이 이상적이다. 하지만 그렇지 못할경우, was/was의 메인 설정은 변경하지말고, 호출하는 방식으로 별도 script로 분리하는것이 운영/개발 에 용이하다.

>###### root 권한방지

간혹, 리눅스에서 root로 tomcat실행하는 경우가있는데, 일단 기본적인 시스템보안을 생각하면 절대로 금지해야 할 사항이다. 이렇게해서 방지하도록 하자

![img-root](https://github.com/agongi/study/blob/master/tomcat/%2311/images/Screen%20Shot%202015-07-23%20at%2011.18.44%20PM.png)

>###### Connector

Connector의 설정 customized를 통해 좀 더 원하는 성능을 낼수도 있다. 다음 그림을 살펴보자.

![img-connector](https://github.com/agongi/study/blob/master/tomcat/%2311/images/Screen%20Shot%202015-07-23%20at%2011.20.18%20PM.png)
