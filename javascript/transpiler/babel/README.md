## Babel

```
ㅁ Author: suktae.choi
ㅁ References
- https://meetup.toast.com/posts/85
```

### Cores

TBD

### Additional

#### Polyfill

`['a', 'b', 'c'].indexOf('b');` 는 IE8에서 오류가 발생한다. '없기' 때문이다. IE8이하는 배열의 요소를 검색할 수 있는 native 함수가 없다. 그래서 보통 같은 동작을 하는 함수를 만들어 프로젝트의 어딘가에 두고 쓴다.

```js
function indexOf(arr, identity) {}

var arr = ['a', 'b', 'c'];
indexOf(arr, 'c'); // 2
```

이런 패턴을 Fallback이라 한다. 없는 기능을 흉내 내어 쓰는 것이다. 하지만 이 코드를 쓰게 되면 indexOf를 native에서 지원하는 브라우저에서도 Fallback을 쓰기 때문에 성능 저하의 원인이 된다. 그래서 다른 방법을 사용한다.

```js
if (typeof Array.prototype.indexOf === 'undefined') {
Array.prototype.indexOf = function() {};
}
```

Array 라는 native 객체에 indexOf 메서드가 없을 경우 아까 만들었던 Fallback으로 대체한다. 이 패턴을 Polyfill이라고 한다. 

이로써 native에 있으면 native 구현을 사용하고 없으면 Polyfill을 쓰게 된다.

#### Plugins

지원하지 않는 기능 (or 신규기능을 미리) 사용하기 위해 선언한다.

