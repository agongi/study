## Factory Pattern
Java design pattern of Factory.

>###### This pattern `separate dependency` between provider and client class.<br> Once class A has been changed, but class B doesn't need to concern of it.<br> (Factory class has all dependencies inside)

```
@author: suktae.choi
- http://blog.jdm.kr/180
```

`public static factory pattern` is especially useful when there is needs of making different instance with the same input parameters. Constructor never separate it but it is simple to make it different with factory pattern as following :

```java
public class Demo {
  private final int a;
  private final int b;

  private Demo(int a, int b) {
    this.a = a;
    this.b = b;
  }

  public static Demo getInstance(int a, int b) {
    return new Demo(a, b);
  }

  public static Demo getInstanceAddSub(int a, int b) {
    return new Demo(a+b, a-b);
  }
}

```

**Quick reference**
```
└── src
    └── com
        └── sec
            ├── Main.java
            ├── builder
            │   └── Person.java
            ├── factory
            │   ├── Apple.java
            │   ├── Fruit.java
            │   ├── FruitFactory.java
            │   └── Melon.java
            └── singleton
                └── Demo.java
```

**Fruit.java**
```java
package com.sec.factory;

public interface Fruit {
	void print();
}
```

**Apple.java**
```java
package com.sec.factory;

public class Apple implements Fruit {

	@Override
	public void print() {
		System.out.println("Apple");		
	}
}
```

**Melon.java**
```java
package com.sec.factory;

public class Melon implements Fruit {

	@Override
	public void print() {
		System.out.println("Melon");		
	}
}
```

**FruitFactory.java**
```java
package com.sec.factory;

public class FruitFactory {
	public static Fruit getInstance(String name) {
		try {
			Class<?> cls = Class.forName(name);
			Object obj = cls.newInstance();

			if(obj instanceof Fruit) {
				return (Fruit) obj;
			}			
		} catch(Exception ex) {
			ex.printStackTrace();
		}

		return null;
	}
}
```

> It uses `public static method` to get instance in factory class. This way is also used in singleton pattern.

> `Reflection` is used in Factory class for avoiding hell of if ~ else statement loop.

**Main.java**
```java
package com.sec;

import com.sec.factory.Fruit;
import com.sec.factory.FruitFactory;

public class Main {
    public static void main (String[] args) throws ClassNotFoundException, InstantiationException, IllegalAccessException {

    	Fruit fruit1 = FruitFactory.getInstance("com.sec.factory.Apple");
    	Fruit fruit2 = FruitFactory.getInstance("com.sec.factory.Melon");

    	fruit1.print();
    	fruit2.print();    	
    }
}
```

**Output**
```
Apple
Melon
```
