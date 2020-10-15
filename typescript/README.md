## TypeScript

```
ㅁ Author: suktae.choi
ㅁ References
- (import, export) https://medium.com/@_diana_lee/default-export%EC%99%80-named-export-%EC%B0%A8%EC%9D%B4%EC%A0%90-38fa5d7f57d4
```

## [Import](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/import)

### default

export 되는 대상이 1개 이므로, 어떤 이름을 지정해도 무엇을 지정하는지 알 수 있다. 그래서 아무 이름이나 지정 가능

```typescript
import FooController from '../foo.controller'
import BarController from '../foo.controller'
```

### named

정의된 이름을 지정해서 import 한다.

```typescript
import * as angular from "angular";
import from '../';
import { foo, bar } from '../foo.controller'
```

정의된 named 전체 or directory 전체 or 특정 이름만 으로 설정이 가능하다.h

## [Export](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/export)

### default

- 1개만 선언 가능
- 이름이 없다의 의미 -> 익명으로 export 한다
- 즉 import 할때 any name 으로 지정 가능하다
  - 어짜피 1개만 export 되므로, 어떤 대상인지 몰라도 된다. 그래서 아무 이름이나 지정 가능
- var, const, let 은 default export 할 수 없다

```typescript
function _foo() {
  return "foo";
}

export default class Bar {
  // ...
}
```

### named

- N개 선언 가능
- 이름이 있다의 의미 -> 이름이 있는 function/class/variable 을 export 한다
- 즉 import 할때 same name 을 지정해야 한다
  - 어떤 type 을 import 할지 정의된 이름을 지정해야 한다
- var, const, let 사용 가능

```typescript
function foo() {
  return "foo";
}

const bar = "bar";

class baz {
  private aaa: number;
}

// export
export { aaa, foo, baz };
```

## [타입선언](https://medium.com/naver-fe-platform/%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC%EA%B0%80-%EB%AA%A8%EB%93%88-%ED%83%80%EC%9E%85-%EC%84%A0%EC%96%B8%EC%9D%84-%EC%B0%B8%EC%A1%B0%ED%95%98%EB%8A%94-%EA%B3%BC%EC%A0%95-5bfc55a88bb6)

- .ts 선언 + 구현
- .js 구현
- .d.ts 선언

