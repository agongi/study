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

#### TypeScript

TypeScript 명세대로 구현하고, 작성된 코드를 native-js 로 transpile 한다.

순수 JS 로 구현하는것 대비 장점은: [Why you shouldn`t be scared of TypeScript](https://han41858.tistory.com/14)

#### CoffeeScript

CoffeeScript 명세대로 구현하면, transpile 시 native-js 를 생성한다.

#### Babel

브라우져 호환성을 위해, 작성된 native-javascript 코드를 (ex. ES6 문법사용) 호환되는 코드로  (ex. ES5) 변환해준다.

#### Traceur

Faster than babel 을 목표로 한다. 목적은 babel 과 동일.

