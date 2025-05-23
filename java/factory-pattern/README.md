# Factory Pattern
```
https://velog.io/@ellyheetov/Factory-Pattern
```

객체는 속성은 변경이 빈번함에 따라 생성자도 같이 변경이 필요합니다.
객체의 생성을 담당하는 클래스를 한 곳에서 관리하여 결합도를 줄이기 위하여 팩토리 패턴이 존재합니다.

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

## Fruit.java
```java
package com.sec.factory;

public interface Fruit {
	void print();
}
```

## Apple.java
```java
package com.sec.factory;

public class Apple implements Fruit {
	@Override
	public void print() {
		System.out.println("Apple");		
	}
}
```

## Melon.java
```java
package com.sec.factory;

public class Melon implements Fruit {
	@Override
	public void print() {
		System.out.println("Melon");		
	}
}
```

## FruitFactory.java
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

## Main.java
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
```
Apple
Melon
```
