## Transpiler

```
ㅁ Author: suktae.choi
ㅁ References
```

**Transpilers**, or source-to-source compilers, are tools that read source code written in one programming language, and produce the equivalent code in another language.

#### Index

- [TypeScript](typescript)
- [Babel](babel)

### Items

#### Vanilla

그냥 순수 JavaScript를 일컫는 말이다. Vanilla는 비격식으로 평범한, 특별할 것 없는 이라는 뜻을 가진 형용사이다.

왜 굳이 별도의 learning-curve 가 있는 대안언어 (ex. TypeScript) 를 학습해야해? 라는 질문의 대답으로 나온 Joke

#### TypeScript

TypeScript 명세대로 구현하고, 작성된 코드를 native-js 로 transpile 한다.

순수 JS 로 구현하는것 대비 장점은: [Why you shouldn`t be scared of TypeScript](https://han41858.tistory.com/14)

#### CoffeeScript/Dart

CoffeeScript/Dart 명세대로 구현하면, transpile 시 native-js 를 생성한다.

#### Babel

브라우져 호환성을 위해, 작성된 native-javascript 코드를 (ex. ES6 문법사용) 호환되는 코드로  (ex. ES5) 변환해준다.

#### Traceur

Faster than babel 을 목표로 한다. 목적은 babel 과 동일.

