## Java serializable
Java fundamental of serializable.

> An object can be represented as a `sequence of bytes` that includes the object's data as well as information about the object's type and the types of data stored in the object to `transfer to other JVM or be stored as file`.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.28
ㅁ Origin: Effective Java 2nd
ㅁ References:
 - http://www.tutorialspoint.com/java/java_serialization.htm
 - http://javarevisited.blogspot.kr/2011/04/top-10-java-serialization-interview.html
```

<img src="https://github.com/agongi/study/blob/master/java/serializable/images/Serialization%20in%20Java.JPG" width="75%">

**What is Serialization**

Simply, `convert object to array of bytes` to store in disk or transfer in network.

 - Simply, `implements java.io.Serializable`
 - All fields are serialized except marked `transient` or `static`

> Static field doesn't belong to object but class. so static variable are not serialized.

**What is serialVersionUID**

SerialVersionUID is included in serialization of object and being checked in deserialization with declared value. If it doesn't match, java will throw `java.io.InvalidClassException`.

**Serializable vs Externalization**

Serializable JVM has full control for serializing object while Externalizable, application gets control for persisting objects.

**Best Practice**
 - Use `custom binary format`

 : Serialized binary format becomes part of Class's exported API and it can potentially break Encapsulation in Java provided by private and package-private fields

 - Define `private static final long serialVersionUID` explicitly

 : Implicitly generated value is very sensitive of modification of object

**VO**
```java
package com.sec.serialization;

import java.io.Serializable;

public class Employee implements Serializable {

	private static final long serialVersionUID = 1L;

	private String name;
    private String address;
    private int SSN;
    private int number;

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
	public int getSSN() {
		return SSN;
	}
	public void setSSN(int sSN) {
		SSN = sSN;
	}
	public int getNumber() {
		return number;
	}
	public void setNumber(int number) {
		this.number = number;
	}

	@Override
	public String toString() {
		return "Employee [name=" + name + ", address=" + address + ", SSN=" + SSN + ", number=" + number + "]";
	}
}
```

**Serialization**
```java
import java.io.*;

public class SerializeDemo
{
   public static void main(String [] args)
   {
      Employee e = new Employee();
      e.name = "Reyan Ali";
      e.address = "Phokka Kuan, Ambehta Peer";
      e.SSN = 11122333;
      e.number = 101;

      try
      {
         FileOutputStream fileOut =
         new FileOutputStream("/tmp/employee.ser");
         ObjectOutputStream out = new ObjectOutputStream(fileOut);
         out.writeObject(e);
         out.close();
         fileOut.close();
         System.out.printf("Serialized data is saved in /tmp/employee.ser");
      } catch(IOException i)
      {
          i.printStackTrace();
      }
   }
}
```

**Deserialization**
```java
import java.io.*;
public class DeserializeDemo
{
   public static void main(String [] args)
   {
      Employee e = null;
      try
      {
         FileInputStream fileIn = new FileInputStream("/tmp/employee.ser");
         ObjectInputStream in = new ObjectInputStream(fileIn);
         e = (Employee) in.readObject();
         in.close();
         fileIn.close();
      } catch(IOException i)
      {
         i.printStackTrace();
         return;
      } catch(ClassNotFoundException c)
      {
         System.out.println("Employee class not found");
         c.printStackTrace();
         return;
      }
      System.out.println("Deserialized Employee...");
      System.out.println("Name: " + e.name);
      System.out.println("Address: " + e.address);
      System.out.println("SSN: " + e.SSN);
      System.out.println("Number: " + e.number);
    }
}
```

**Output**
```java
Deserialized Employee...
Name: Reyan Ali
Address:Phokka Kuan, Ambehta Peer
SSN: 0
Number:101
```
