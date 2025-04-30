---
title: Scala 3 Basics
date: 2022-09-13T09:38:50+05:30
slug: scala-3-basics
categories:
  - Big Data
tags:
  - scala
---
## Scala Basics
### How to declare a variable
#### Declare a constant variable
```scala
val x: Int = 42
```
or

```scala
val x = 42
```
  
* types are optional
* val cannot be reassigned, val are constant/immutable
* Semicolon are not necessary

#### Declare a variable
```scala
var x: Int = 42
```
or

```scala
var x = 42
```
#### Expressions & Instructions
* Changing a variable is called as side effects
* Instructions are doing something.
* Expression is something that has a value.
* Donot write loops in Scala. Everything in Scala is an expression.
* There is a special data type in Scala that is `Unit`. Unit is something like inline function. eg `val x = while(i<10){println(i);i++}`
* Instructions are executed (Java). Expressions are evaluated (Scala).
#### Code Blocks
```scala
val codeBlock = {
    val z = 2;
    val w = z + 3;
    if (w > 3) "hello" else "goodbye"
}
```
* Code block is an expression.
* The value of the whole block is the value of its last expression.

### Functions
#### How  to define functions
```scala
def aFunction(a: String, b: Int): String = {
    a + " " + b
}
def aFunction1(a: String, b: Int): String = a + " " + b
def aFunction2(a: String, b: Int): String = {
   if (b == 1) a
   else a + aFunction2(a, b - 1) 
}
```
* An expression or a code block.
* Parameterless functions can be called just by their names (ie. without using parenthesis ())
* In Scala when you need loops, use recursions. This is a fundamental idea of functional programming.
* The compiler can infer the return type of a function, but we cannot figure out the return type of a recursive function. As best practice always specify the return type.
* You can also return Unit.
#### Interpolators
```scala
def play(name: String, age: Int) = println(s"Hi my name is $name and my age is $age")
def main(args: Array[String]): Unit = {
	play("Ashish", 31)
}
```
#### Tail Recursion (@tailrec)
Write recursion in the last expression. The idea used by compilers to optimize tail-recursive functions is simple, since the recursive call is the last statement, there is nothing left to do in the current function, so saving the current function’s stack frame is of no use.
```scala
object Playground {
	def concat(str: String, n: Int): String = {
		@tailrec
		def concat(str: String, word: String, n: Int): String = {
			if (n == 1) str
			else concat(str + word, word, n - 1);
		}
		concat(str, str, n);
	}
	def main(args: Array[String]): Unit = {
		println(concat("Ashish", 5))
	}
}
```
#### Call by name and call by value
* In call by value, the expression is computed before passed to the function.
* In call by name, the expression is passed as it is and is calculated every time it is being used.
* Call by name can be used for lazy evaluations.
```scala
object Playground {
	def callByValue(n: Long) = {println(n);println(n)}
	def callByName(n: => Long) = {println(n);println(n)}
	def main(args: Array[String]): Unit = {
		callByValue(System.nanoTime())
		/* Output -
			10957615477957
			10957615477957
		*/
		callByName(System.nanoTime())
		/* Output -
			10957771851376
			10957771908322
		*/
	}
}
```
#### Default Parameters
Scala allows us to specify default value of a parameter. This is helpful in case of accumulators (tail recursion).
```scala
def fact(n: Int, acc: Int = 1): Int
```
##### Named Parameter
```scala
def fact(n: Int, acc: Int = 1): Int
fact(acc = 12, n = 10);
```

## Object Oriented
### Obect Oriented Basic
* [Scala Constructors](https://www.geeksforgeeks.org/scala-constructors/)
### Syntactic Sugar: Method Notations
* In Scala All operators are methods
* In Scala every unary operators are methods
* Functions which doesn't have any parameter can be used as postfix unary operators.
* There is a special method called as `apply()`

```scala
import scala.language.postfixOps

class Person {
	//prefix
	def unary_! = "This is a string"
	//postfix
	def isAlive: Boolean = true
	//directly calling object()
	def apply(): String = "Hi buddy"
}

@main
def main(): Unit = {
	val marry = new Person
	//prefix
	println(!marry)
	//postfix
	println(marry isAlive)
	//effect of apply method
	println(marry())
}
```
```scala
import scala.language.postfixOps

class Person(val name: String, var age: Int = 0) {
	def unary_! = "This is a string"
	def +(postfix: String): Person = new Person(s"${this.name} is a ${postfix}")
	def ++ = age = age + 1;
	def learns(str:String):String = s"${this.name} learns ${str}"
	def apply(num: Int): String = s"${this.name} watched inception ${num} times"
}

@main
def main(): Unit = {
	val ashish = new Person("Ashish")
	ashish++;
	println(ashish.age)
	println(ashish learns "Scala")
	println(ashish(2))
}
```
### Scala Objects
* Scala doesn't have the concept of `static` (class level functionality). The alternative is declare as object
* Scala object is like a singleton instance
	```scala
	object Person {
		var totalPerson: Integer
	}
	```
* Scala Applications are either declared using main or by extending App
	```scala
	object Person extends App {
		var counter: Integer = 0
		val ashish = new Person("Ashish")
		println(ashish.age)
		println(ashish learns "Scala")
		println(ashish(2))
	}
	@main
	def main(): Unit = {
		val ashish = new Person("Ashish")
		println(ashish.age)
		println(ashish learns "Scala")
		println(ashish(2))
	}
	```
### Overriding & Inheritence
* Use keyword `override`
* Using `sealed` keyword in class definition, we can allow a class to get extended in the same file only.

### Abstract classes vs traits
* Abstract classes can have both abstract and non abstract methods
* A trait is like an interface with a partial implementation. In scala, trait is a collection of abstract and non-abstract methods. You can create trait that can have all abstract methods or some abstract and some non-abstract methods.
* Traits donot have constructor parameters
* Traits can be extended using `with`
* Can extend only one abstract class but we can extend multiple traits

### Generics
* In Scala generics are defined using [T]   ie. square brackets
