## Primitive type vs Boxed Primitives
Java conceptual comparison between Primitive type and Boxed Primitives.

>###### Use primitive type unless boxed primitives is required in certain cases.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.18
ㅁ References:
 - https://msdn.microsoft.com/en-us/library/yz2be5wk.aspx
 - http://www.jpstory.net/2013/02/primitive-vs-boxed-primitives/
 - http://truepia.tistory.com/185.
 - http://javarevisited.blogspot.kr/2012/07/auto-boxing-and-unboxing-in-java-be.html
```

#### 1. Primitive type
Stored in stack as local variable.

 - Type
 - Initialized in default value (e.g. 0)
 - `Faster than` boxed primitives in calculation

> Use primitive type in normal case unless boxed primitives is required.

#### 2. Boxed Primitives
Stored in heap as object.

 - Object
 - Initialized in `null`
 - `Slower than` primitive type in calculation
 - Used in `Generics` as parameter
 - Used in `Collections` as parameter

  : Collection internally use `Generic` as a type of parameter

 - Supports useful method in class level (e.g. parseInt, max)

> Use boxed primitive only in certain cases.

#### 3. Autoboxing vs Unboxing

Boxing is the process of converting a value type to the type object or to any interface type implemented by this value type. When the CLR boxes a value type, it wraps the value inside a System.Object and stores it on the managed heap. Unboxing extracts the value type from the object. Boxing is implicit; unboxing is explicit.

 - Boxing : Boxing is used to store value types in the garbage-collected heap. Boxing is an implicit conversion of a value type to the type object or to any interface type implemented by this value type. Boxing a value type allocates an object instance on the heap and copies the value into the new object.

 - Unboxing : Unboxing is an explicit conversion from the type object to a value type or from an interface type to a value type that implements the interface. An unboxing operation consists of :

  : Checking the object instance to make sure that it is a boxed value of the given value type.<br>
  : Copying the value from the instance into the value-type variable.

> **Performance** : In relation to simple assignments, boxing and unboxing are computationally expensive processes. When a value type is boxed, a new object must be allocated and constructed. To a lesser degree, the cast required for unboxing is also expensive computationally.

```java
Integer i = 42; // auth-boxing to assign value to object
i = i + i + i + i + i; // each of operator need to extract value from object calling auto-unboxing
```
 - auto-boxing : 2 times
 - auth-unboxing : 5 times
