## try-with-resources
Java fundamental of try-with-resources.

> The try-with-resources statement lets you free from closing instance resources explicitly by closing it automatically.

```
ㅁ Author: suktae.choi
- https://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html
- http://d2.naver.com/helloworld/1219
```

### 1. Concept
A resource is an object that must be closed after the program is finished with it. The try-with-resources statement ensures that each resource is closed at the end of the statement.

> Any object that implements java.lang.AutoCloseable, which includes all objects which implement java.io.Closeable, can be used as a resource.

#### java.io.Closeable
```java
public interface Closeable extends AutoCloseable {
    public void close() throws IOException;
}
```

#### java.lang.AutoCloseable
```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

### 2. Usage
#### 2.1. How to use
The resource which is declared just after try keyword as the parameter will be able to used as try-with-resources.

```java
static String readFirstLineFromFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    } catch (Exception ex) {
      // ...
    }
}
```

This is equal to traditional java finally() statement as following:

```java
static String readFirstLineFromFileWithFinallyBlock(String path) throws IOException {
    BufferedReader br = new BufferedReader(new FileReader(path));

    try {
        return br.readLine();
    } catch (Exception ex) {
      // ...
    }
    finally {
        if (br != null) br.close();
    }
}
```

#### 2.2. Multiple use
You may declare one or more resources in a try-with-resources statement. It can contain two declarations that are separated by a semicolon;

Those methods are called automatically in order(BufferReader -> ZipFile). close method is called in the opposite order of this creation.

```java
static String readFirstLineFromFile(String path) throws IOException {
  try (BufferedReader br = new BufferedReader(new FileReader(path));
      ZipFile zf = new java.util.zip.ZipFile(zipFileName)) {
        return br.readLine();
    } catch (Exception ex) {
      // ...
    }
}
```

### 3. What is difference?
When the methods readLine and close both throw exceptions,

 - try-with-resources

 : The exception thrown from the `try-with-resources block is suppressed`.

 > The exception within try block will be caught as intended by program.

 - finally

 : The exception thrown from the `try block is suppressed`.

 > Program intent to catch exceptions in try block. but both exceptions are thrown, the exception from try block is suppressed.


### 4. Usage with tradition finally statement
A try-with-resources statement can have catch and finally blocks just like an ordinary try statement. In a try-with-resources statement, any catch or finally block is run after the resources declared have been closed.

> try-with-resources is executed to close resources at first, then other catch-finally blocks will be called.
