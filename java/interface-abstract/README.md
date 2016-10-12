## Interface vs Abstract
Java conceptual comparison between Interface and Abstract class.

>A class which implements interface should `@override` **all methods** declared. But a class which extends abstract class only needs to `@override` **some of them** declared abstract.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.22
ㅁ Origin: Effective Java 2nd edition
ㅁ References:
 - https://docs.oracle.com/javase/tutorial/java/IandI/abstract.html
```

### 1. Interface
 - Field type<br>
 : public<br>
 : static<br>
 : final

 - Method type<br>
 : declaration<br>
 : implementation

 - implements (1-1)

```java
interface GraphicObject {
     int x, y;
     ...
     void moveTo(int newX, int newY);
     void draw();
     void resize();
}
```

```java
class Circle implements GraphicObject {
  void moveTo(int newX, int newY) { ... }
  void draw() { ... }
  void resize() { ... }
}
class Rectangle implements GraphicObject {
  void moveTo(int newX, int newY) { ... }
  void draw() { ... }
  void resize() { ... }
}
```

### 2. Abstract
  - Field type<br>
  : public, protected, private<br>
  : static, non-static<br>
  : final, non-final

  - Method type<br>
  : declaration

 - extends (1-N)

```java
abstract class GraphicObject {
    int x, y;
    ...
    void moveTo(int newX, int newY) { ... }
    abstract void draw();
    abstract void resize();
}
```

```java
class Circle extends GraphicObject {
  void draw() { ... }
  void resize() { ... }
}
class Rectangle extends GraphicObject {
  void draw() { ... }
  void resize() { ... }
}
```

### 3. When an Abstract Class implements an Interface

```java
/* A class with declared abstract doesn't need to implement all of methods in interface not like normal class */
abstract class X implements Y {
  // implements all but one method of Y
}

class XX extends X {
  // implements the remaining method in Y
}
```

> Abstract and Interface both could **not be created stand-alone using new keyword**. interface needs to be implemented and abstract being extends.
