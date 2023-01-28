## Java final
Java fundamental of final keyword.

> keyword final has miscellaneous meaning in each locations. e.g. primitive, object, class and method.

```
ㅁ Author: suktae.choi
- http://stackoverflow.com/questions/4012167/java-final-modifier
- http://stackoverflow.com/questions/2435163/why-can-final-object-be-modified
```

#### Primitive
```
can be set only once. (memory and performance gain [immutable])
```

#### Method
```
can't be @overriden
```

#### Object
```
can be modified.
object reference never be changed.

<Example> - java

final Object obj = new Object();
obj.setA("aa"); // okay

Object obj2 = new Object();
obj = obj2; // fail
```

#### Class
```
can't be extended
```
