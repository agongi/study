## Number

```
ㅁ Author: suktae.choi
- https://docs.oracle.com/javase/tutorial/java/data/converting.html
```

### Classes

<img src="images/Screen%20Shot%202016-03-11%20at%2011.41.54.gif" width="75%">

> BigDecimal & BigInteger are used for **high-precision** calculations. AtomicInteger & AtomicLong are used for **multi-threaded** applications

### Converting
#### String to Number
```java
String number = "5";

// to double primitives
double primitiveNumber = Double.parseDouble(number);

// to boxed double
Double boxedNumber = Double.valueOf(number).doubleValue();

```

#### Number to String
```java
double number = 5;

// commonly used
String a = number.toString();

// if you want
String b = String.valueOf(number);
```
