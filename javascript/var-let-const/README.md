## `var` vs `let` vs `const`

```
ㅁ Author: suktae.choi
ㅁ References:
- https://gist.github.com/LeoHeo/7c2a2a6dbcf80becaaa1e61e90091e5d
- https://velog.io/@bathingape/JavaScript-var-let-const-%EC%B0%A8%EC%9D%B4%EC%A0%90
- https://evan-moon.github.io/2019/06/18/javascript-let-const/
```

## var

- function scope
- 중복선언 가능
- 재할당 가능 (== mutable)

## let

- block scope
- 중복선언 불가능
- 재할당 가능 (== mutable)

## const

- block scope
- 중복선언 불가능
- 재할당 불가능 (== immutable)

***

## Hoisting

모두 hoisting 대상이다. 다만 function, block scope 에 따른 동작 차이는 존재한다.

## TDZ (Temporal Dead Zone)

let, const 모두 block-scope hoisting 이 발생하는데, 아래 코드는 compile-error 가 발생한다.

```javascript
console.log(bar); // Error: Uncaught ReferenceError: Cannot access 'bar' before initialization
let bar;
```

> hoisting 이 발생하면 var 와 마찬가지로 let 도 동작해야함

이유를 알기위해 우선 V8 엔진의 변수 생성과정을 확인해보겠습니다.

- Declaration: 선언
- Initialization: 메모리할당 & undefined 세팅
- Assignment: 값의 할당

var 의 동작을 확인해보면

```javascript
/* var foo; // hoisting:: 선언과 초기화가 수행됨 */

console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1;	// 할당
console.log(foo); // 1
```

> 선언과 초기화가 같이 수행됨

let 의 동작입니다.

```javascript
/* var foo; // hoisting:: 선언만 수행됨 */

console.log(foo); // Error: Uncaught ReferenceError: Cannot access 'foo' before initialization

var foo;	// 초기화
console.log(foo); // undefined

foo = 1;	// 할당
console.log(foo); // 1
```

> 선언과 초기화가 분리됨

호이스팅부터 - 변수선언 까지의 (즉 초기화 안된) 구간을 `TDZ` 이라고 부릅니다.

