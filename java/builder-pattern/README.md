## Builder pattern
Java design pattern of Builder.

>###### It is useful when a number of parameters of instance constructor is more than 3 or above.

```
ㅁ Author: suktae.choi
ㅁ Date: 2016.02.12
ㅁ References:
 - http://chimera.labs.oreilly.com/books/1230000000545/ch02.html
 - http://cleancodes.tistory.com/m/post/15
```

**Person.java**
```java
package com.sec.builder;

public class Person {
	static {
		System.out.println("static initializer Person");
	}

	private final String name;
	private final int age;
	private final String addr;
	private final int zipCode;

	private Person(PersonBuilder builder) {
		System.out.println("instance constructor Person");

		this.name = builder.name;
		this.age = builder.age;
		this.addr = builder.addr;
		this.zipCode = builder.zipCode;
	}

	public static class PersonBuilder {
		static {
			System.out.println("static initializer PersonBuilder");
		}		
		// mandatory
		private String name;
		private int age;
		// optional
		private String addr = null;
		private int zipCode = 0;

		public PersonBuilder(String name, int age) {
			this.name = name;
			this.age = age;
		}

		public PersonBuilder addr(String addr) {
			this.addr = addr;
			return this;
		}
		public PersonBuilder zipCode(int zipCode) {
			this.zipCode = zipCode;
			return this;
		}

		public Person build() {
			return new Person(this);
		}		
	}

	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + ", addr=" + addr + ", zipCode=" + zipCode + "]";
	}
}
```

**Main.java**
```java
package com.sec;

import com.sec.builder.Person;

public class Main {
    public static void main (String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException {

      Person person1 = new Person.PersonBuilder("suktae", 30)
                      					 .addr("kr")
                      					 .zipCode(11111)
                      					 .build();

    	Person person2 = new Person.PersonBuilder("company", 55)
							                   .build();

    	System.out.println(person1);
    	System.out.println(person2);
    }
}
```

**Output**
```
static initializer PersonBuilder
static initializer Person
instance constructor Person
instance constructor Person
Person [name=suktae, age=30, addr=kr, zipCode=11111]
Person [name=company, age=55, addr=null, zipCode=0]
```

**Explanation**
```
1) Instance is created of class PersonBuilder with mandatory parameters
 : new Person.PersonBuilder("suktae", 30)

 <serializable-begin>
  static initializer is executed
  static variable is initialized
 <serializable-end>

2) Each method of class PersonBuilder returns itself to keep going on tailing with optional parameters or not
 : .addr("kr")
   .zipCode(11111)

3) Instance is created of class Person
 : .build();

 <serializable-begin>
  static initializer is executed
  static variable is initialized
 <serializable-end>

 private constructor is loaded with parameter of class PersonBuilder
 ```

**Pros**
 - Especially useful that instance constructor parameters are more than 3 or above<br>
  (`readability`, ignore-parameter ordering and `avoid human error`)

 - It could be `immutable` with final modifier in variables of upper class<br>
  (private final String name)

**Cons**
 - always need to create `instance of Builder` to make `instance of class`<br>
  (instance creation cost is increased)
