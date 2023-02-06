## Aggregate Repository

```
@author: suktae.choi
- https://www.popit.kr/%ec%97%90%ea%b7%b8%eb%a6%ac%ea%b2%8c%ec%9e%87-%ed%95%98%eb%82%98%ec%97%90-%eb%a6%ac%ed%8c%8c%ec%a7%80%ed%86%a0%eb%a6%ac-%ed%95%98%eb%82%98/
```

즉 요약하자면, 

- Aggregate Root 는 Repository 제공
- 동일 도메인의 Aggregate 는 Root 에서 Cascade = ALL 을 통해 간접적으로 CUD 를 처리한다.

