# final
```
https://stackoverflow.com/questions/4012167/java-final-modifier
https://stackoverflow.com/questions/2435163/why-can-final-object-be-modified
````

## Class
상속 불가

## Method
재정의 불가 (@Override)

## Variable
### Primitive
재정의 불가 (== 값의 변경불가)

### Object/Collection
레퍼런스 변경 불가 (== 값은 변경가능)
```java
final Object obj = new Object();
obj.setA("aa"); // okay

Object obj2 = new Object();
obj = obj2; // fail
```