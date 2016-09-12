## Java Exception
Java fundamental of Exception.

>###### Simply describe how expensive throwing exception and best practice to handle exception in java.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.27
ㅁ References:
 - http://java-performance.info/throwing-an-exception-in-java-is-very-slow/
 - http://thswave.github.io/java/exception/2015/06/28/exceptions-are-bad.html
 - http://www.nextree.co.kr/p3239/
 - https://dzone.com/articles/java-top-5-exception-handling
```

#### 1. What is Exception
Exception is an useful way to signal that a routine could not execute normally.
 - input argument is invalid
 - a resource it relies on is unavailable
 - a condition it executed in unstable

One mechanism to transfer control, or raise an exception, is known as a `throw`. The exception is said to be thrown. Execution is transferred to a `catch`. The exception could be caught in `try` scope.

```java
public static void main(String[] args) {
  try {
    // ... normal routine
  } catch(Exception ex) {
    // ... exception routine when Exception occurred
    ex.printStackTrace();
  }
}
```

#### 2. CheckedException vs UncheckedException
CheckedException
 - `compile-time` exception
 - a subclass of `Exception`
 - a SDK that throws exception, then consumer class needs to `try ~ catch` that exception explicitly

UncheckedException
 - `run-time` exception a subclass of `RuntimeException`
 - a runtime exception couldn't be caught in normal flow of program

<img src="https://github.com/agongi/study/blob/master/java/exception/images/Screen%20Shot%202016-02-27%20at%2019.27.08.png" width="75%">

#### 3. Best Practice
###### 3.1. Avoid catching Throwable and Errors
```java
try {
  // ...
} catch(Throwable ex) {
  logger.error("some context message", ex); // Non-Compliant. Throwable is the superclass of all errors and exceptions
}
```

###### 3.2. Use logging framework instead of normal print
```java
try {
  // ...
} catch(Exception ex) {
  console.log("some context message"); // Difficult to Retrieve Logs for Debugging
}

try {
  // ...
} catch(Exception ex) {
  logger.error("some context message", ex); // Compliant
}
```

###### 3.3. Avoid throwing Exception/RuntimeException
```java
public void foo() throws Throwable {...} // Non-compliant

public void foo() throws Exception {...} // Non-compliant

public void foo() {
  throw new RuntimeException("My Message"); // Non-Compliant
}
public void foo() {
  throw new CustomRuntimeException("My Message"); // Compliant
}
```

###### 3.4. Printing properly
```java
try {
  // ...
} catch(Exception ex) {
  logger.error("some context message"); // The exception is lost
}

try {
  // ...
} catch(Exception ex) {
  logger.error(ex); // No context message
}

try {
  // ...
} catch(Exception ex) {
  logger.error(ex.getMessage()); // The exception is lost. Just that exception message is written
}

try {
  // ...
} catch(Exception ex) {
  logger.error("some context message", ex); // Context message is there. Also, exception object is present
}

try {
  // ...
} catch(Exception ex) {
  throw new CustomRuntimeException("context", ex); // Context message is there. Also, exception object is present
}
```

###### 3.5. Using Cache
```java
public static final CustomException exceptionA = new CustomException(ResponseType.A);
```
