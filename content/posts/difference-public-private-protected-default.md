---
title: What is the difference between public, private, protected and default?
date: 2016-09-27T07:19:38+05:30
lastmod: 2016-09-27T15:39:36+05:30
description: Types of Access modifiers in Programming languages. access control in java, access specifiers in java, public class java, java method permissions
slug: difference-public-private-protected-default
categories:
  - Java
---
Public, Private and Protected are known as Access Modifiers. In most programming languages there is another access modifier and that is known as 'default'. In C++ there are 3 access modifies public, private and protected. In Java there are four access modifier public, private, protected and default.

In java if we don't specify any access modifier in methods or fields, it assumes as 'default' but in C++ it assumes as 'private'.

First of all let me specify all the four in short and then explain in detail.

**Public ::** All other classes and methods can access.  
**Private ::** No other classes and methods could access.  
**Protected ::** Only those classes who inherited that class or is in the same package of that class could access.  
**Default ::** Classes which are in the same package as that class could access.

## Public

Lets say I have class called as `Bar` in package `com.csetutorials`

```java
package com.csetutorials;
public class Bar {
	public int counter;
}

```


So in this case I could change the value of field `counter` from any class any method.

```java
package com.csetutorials;
public class Foo {
	public static void main(String[] args) {
		Bar bar = new Bar();
		bar.counter = 10;
		System.out.println(bar.counter); // Output: 10
	}
}
```


If we run the class `Foo` it will display the output '10' stats that we can change its value

## Private

In the above examples in class `Bar`, change the access modifier of field `counter` from public to private 

```java
package com.csetutorials;
public class Bar {
	private int value;
}
```


Now run the class `Foo` and see what happens. You will see the following error

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problems: 
	The field Bar.counter is not visible
	The field Bar.counter is not visible

	at com.csetutorials.Foo.main(Foo.java:7)

```


You could see that in this case we have changed the accessability of field `counter` in class `Bar`. Now no method from any other class could aceess or modify the value of `counter` field. Only the members (fields and method) of class `Bar` can change or modify the value of `counter`.

Here is the example : 

```java
package com.csetutorials;
public class Bar {
	private int counter;
	public int getCounter() {
		return counter;
	}
	public void setCounter(int counter) {
		this.counter = counter;
	}
}
```


In the above example `getCounter()` method can access `counter` field and `setCounter()` method can change the value of `counter`. We can call these methods (since they are both public) to set and get the value of `counter`. Here is the example below.

```java
package com.csetutorials;
public class Foo {
	public static void main(String[] args) {
		Bar bar = new Bar();
		bar.setCounter(10);
		System.out.println(bar.getCounter()); // Output: 10
	}
}
```


## Protected

Protected falls somewhere between Private and Public. Any method or field who are protected can be accessed or modified by only those classes who extends their class and not any other. If it doesn't inherit that class then both the classes should be in same package to access that class member.

Example :

```java
package com.csetutorials;
public class Bar {
	protected int counter;
}
```


```java
package com.csetutorials;
public class Foo {
	public static void main(String[] args) {
		Bar bar = new Bar();
		bar.counter = 10;
		System.out.println(bar.counter); // Output: 10
	}
}
```


As you can see `Foo` class can access `counter` because both the classes `Bar` and `Foo` are in the same package 'com.csetutorials' but suppose there is a class which is not in package `com.csetutorials` then it can't access `counter` directly.

```java
package com.mypackage;
import com.csetutorials.Bar;
public class Test {
	public static void main(String[] args) {
		Bar bar = new Bar();
		bar.counter = 10;
		System.out.println(bar.counter);
	}
}
```


In this case java will throw exception

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problems: 
	The field Bar.counter is not visible
	The field Bar.counter is not visible

	at com.mypackage.Test.main(Test.java:9)
```


So to access the `counter` field  
Lets suppose we can't change the class Bar. Therefore what we do is we create a class lets say `FooBar` in package `com.mypackage` and `FooBar` will extend the class `Bar` because classes that extends a class can access its protected members.

```java
package com.mypackage;
import com.csetutorials.Bar;
public class FooBar extends Bar {
	public int getCounterFromBar() {
		return counter; // return super.counter;
	}
	public void setCounterForBar(int x) {
		counter = x;  //  super.counter = x;
	}
}
```


```java
package com.mypackage;
public class Test {
	public static void main(String[] args) {
		FooBar foobar = new FooBar();
		foobar.setCounterForBar(20);
		System.out.println(foobar.getCounterFromBar()); // output : 20
	}
}
```


When we run Test class, output will be 10

## Default

If a class member is default then it can only be accessible by those class that are in the same package.

If we don't specify an access modifier then java automatically assumes that member as default, but C++ assumes as private.

Example :

```java
package com.csetutorials;
public class Bar {
	int counter;
}
```


In Java, here,in class `Bar` we haven't specified whether `counter` field is public, private or protected. So it assumes `counter` field as default.

```java
package com.csetutorials;
public class Foo {
	public static void main(String[] args) {
		Bar bar = new Bar();
		bar.counter = 10;
		System.out.println(bar.counter); // Output: 10
	}
}
```


So `Foo` class can access `counter` field.

```java
package com.mypackage;
import com.csetutorials.Bar;
public class Test {
	public static void main(String[] args) {
		Bar bar = new Bar();
		bar.counter = 10;
		System.out.println(bar.counter); // Output: 10
	}
}
```


So `Test` class can't access `counter` field because its not in the same package and will throw exception while compiling.

```java
Exception in thread "main" java.lang.Error: Unresolved compilation problems: 
	The field Bar.counter is not visible
	The field Bar.counter is not visible

	at com.mypackage.Test.main(Test.java:9)
```


**NOTE**
**1.** In case of C++ even `Foo` class can't access `counter` field because it becomes public.  
**2.** All the above conditions also applies to methods(function) also.