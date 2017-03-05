## Java serializable
An object can be represented as a `sequence of bytes` that includes the object's data as well as information about the object's type and the types of data stored in the object to `transfer to other JVM or be stored as file`.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.28
ㅁ Origin: Effective Java 2nd
ㅁ References:
 - http://www.tutorialspoint.com/java/java_serialization.htm
 - http://javarevisited.blogspot.kr/2011/04/top-10-java-serialization-interview.html
```

<img src="https://github.com/agongi/study/blob/master/java/serializable/images/20151210_130446.png" width="75%">

### 1. Serialization
Simply, `Convert object to array of bytes` to store in disk or transfer in network.

 - Impacts on `implements java.io.Serializable`
 - All fields are serialized except marked `transient` or `static`

> Static field doesn't belong to instance but class. so static variable are not serialized.

### 2. serialVersionUID
SerialVersionUID is included in serialization of object and being checked in deserialization with declared value. If it doesn't match, java will throw `java.io.InvalidClassException`.

> Serializable `JVM has full control for serializing` object while Externalizable, Application gets control for persisting objects.

### 3. Why declare serialVersionUDI explicitly
serialVersionUID is checked while deserialization in store in file system or transfer to network. JVM automatically generate UID value based on its algorithm and might be vary each JVM's version.

It causes unexpected `InvalidClassException` once It tries to read stored data after upgrading JVM version or different client with its own JVM's.

So Don't forget to declare **private static final long serialVersionUID** in DAO.
