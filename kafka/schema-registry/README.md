# Schema Registry

```
@author: suktae.choi
```

일반적인 데이터의 흐름에서 consumer 는 producer 의 이벤트를 `일방적으로 신뢰` 할 수 밖에 없습니다.

이 구조를 해결하기 위해 producer 와 consumer 는 schema registry 를 이용해 스키마를 등록하고, 이를 통해 정의된 데이터만 주고받게 됩니다.