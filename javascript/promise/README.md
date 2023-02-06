## Promise

```
@author: suktae.choi
- https://stackoverflow.com/questions/17308172/deferred-versus-promise
- https://xebia.com/blog/promises-and-design-patterns-in-angularjs/
```

## Deferred

Promise 를 만드는 Factory Object 이다.

Promise 를 내부에서 직접 생성해서 리턴하는 유형 (ex. $resource) 은 불필요하지만, 외부에서 만들어서 줘야할때는 Q library (or lightweight angular implementation \$q) 를 사용한다.

> 상태는 `resolved` or `rejected` 로 제어

```typescript
// angular
function getPromise(): angular.IPromise<any> {
  let deferred = $q.defer();
  // .. do something
	this.$timeout(() => {
  	deferred.resolve('Succeed');
	}, 1000);

  return derferred.promise;
}
```

## Promise

A **promise** is a promise about future value like java `Future`.

```typescript
function getValue(): string {
  this.getPromise().then((data) => {
    console.log('before:', data);
  });
}
```

## Promise vs Callback

- Promise
  - 보이기엔 약간 더 복잡함
  - Chaining 가능 (다른 Promise 와 #then#then 으로 연결)
- Callback
  - 깔끔하게 callback 으로 받아서 처리
  - Chaining 불가능

