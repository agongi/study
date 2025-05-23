# final
```
https://stackoverflow.com/questions/4012167/java-final-modifier
https://stackoverflow.com/questions/2435163/why-can-final-object-be-modified
````

## Primitive
재정의 불가 (immutable)

## Method
재정의 불가 (@Override)

## Object
레퍼런스 변경 불가 (mutable)
```java
final Object obj = new Object();
obj.setA("aa"); // okay

Object obj2 = new Object();
obj = obj2; // fail
```

## Class
상속 불가
