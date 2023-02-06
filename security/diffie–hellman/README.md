## Diffie–Hellman
Diffie–Hellman key exchange (D–H) is a specific method of securely exchanging cryptographic keys over a public channel.

```
@author: suktae.choi
 - https://ko.wikipedia.org/wiki/%EB%94%94%ED%94%BC-%ED%97%AC%EB%A7%8C_%ED%82%A4_%EA%B5%90%ED%99%98
```

<img src="images/Screen%20Shot%202016-03-01%20at%2018.37.01.png">

디피-헬만 키 교환은 통신을 하는 대상과 비밀 정보를 공유할 수 있지만, `상대방에 대한 인증은 보장되지 않으며` 중간자 공격이 가능하다. 앨리스와 밥이 상대방에 대한 인증을 하지 못할 경우, 공격자는 중간에서 통신을 가로채 앨리스와 공격자, 그리고 공격자와 밥 사이에 각각 두 개의 디피 헬만 키 교환을 생성하고, 앨리스와 밥이 각각 서로와 통신을 하는 것처럼 위장할 수 있다. 이와 같은 종류의 중간자 공격을 막기 위한 여러가지 다른 알고리즘이 개발되어 있다. 인증에 대한 부분을 보안하기 위해, 공개키 암호 방식인 RSA가 제안되었다.
