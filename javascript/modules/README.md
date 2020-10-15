## Modules

```
ㅁ Author: suktae.choi
ㅁ References
- https://d2.naver.com/helloworld/12864
```

#### Blog

- [RequireJS](https://d2.naver.com/helloworld/591319)

***

## CommonJS

JavaScript를 브라우저에서뿐만 아니라, 서버사이드 애플리케이션이나 데스크톱 애플리케이션에서도 사용하려고 조직한 자발적 워킹 그룹이다.

```javascript
// utils.js
const PI = 3.14;
module.exports.PI = PI;

// app.js
const utils = require('./utils');
console.log(utils.PI); // 3
```

> Node.JS accepts CommonJS

## ~~AMD (Asynchronous Module Definition)~~

AMD에서는 비동기 모듈(필요한 모듈을 네트워크를 통해 내려받을 수 있도록 하는 것)에 대한 표준안을 다루고 있다. 물론 CommonJS도 비동기 상황을 고려한 모듈 전송 포맷을 제공하지만, 순수 AMD를 지지하는 사람들과 합의를 도출해 내지는 못했다.

```javascript
require "/src/lib/..";
```

> RequireJS is an implementation of AMD

## ESM (ES6 modules)

ES6 에서 지원하는 native-javascript module 스펙이다.

```javascript
// utils.js
const var1;
let var2;

export function print (msg) {};
export {var1, var2}

// app.js
import util from './util';

console.log(util.var1);
util.print("hello world!");
```

### Comparison

신규로 진행한다면 ES6 명세를 따르는게 맞고, 그렇다면 native-supported 인 ESM 을 사용하자.

그게 아니면 node.js 에서 이미 사용하는 CommonJS 스펙을 따르는게 맞다. ~~(AMD...)~~

### 