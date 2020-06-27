---
layout: post
title: "reflection-in-java"
date: 2018-03-19 20:40:25 +0800
comments: true
categories: "java"
---
## 前言
java的反射机制是java的一个重要的特性，使用反射机制可以使java具有更好的灵活性。有时候我们需要动态的获取类的信息而这些信息有可能是在编译时是获取不到的，这个使用使用反射机制就能很好的解决问题。

## RTTI
RTTI的含义：在运行时识别一个对象的类型。
### class对象
类时程序的一部分，每个类都有一个Class对象，每当编写并编译一个新类就会产生一个Class对象。  
Class对象仅在需要的时候被加载，Class.forName()是Class的一个静态方法，该方法参数为对象的名称，返回的为Class对象的引用。

```java
try {
			Class c=Class.forName("X");
		} catch (ClassNotFoundException e) {
			// 当找不到对应的类时就会报错
			e.printStackTrace();
		}
```
无论何时，只要你想在运行时使用类型信息，就必须首先获得恰当的Class对象的引用，Class.forName()就是实现此功能的便捷途径。使用Class.forName()你不必持有一个该类型的对象，只需要知道类型的名称就可以了，当然如果你持有该类型的对象，你也可以调用对象的getClass方法：

```java
x.getClass()
```
x是对象。  
通过Class对象你可以发现你想要了解的所有信息：

* 类的简单名称和权限定名称
* 类的直接基类
* 类实现的接口
* ···

**类字面常量：**

java提供了另一种方法来生成对Class对象的引用，就是类字面常量

```java
X.class
```
X是类而不是对象。  
与使用Class.forName()相比，使用类字面常量有以下的优点：

1. 更加简单和安全，因为在编译时就受到检查。
2. forName()是方法调用，所以类字面常量更加高效。
3. 使用类字面常量时不会自动初始化该Class对象。

类字面常量不仅可以应用于普通的类，也可以应用于接口、数组以及基本数据类型。另外对于基本数据类型的包装类，还有一个标字段TYPE。TYPE字段是一个引用，指向对应的基本数据类型的额Class对象，如下所示：

|boolean.class	|Boolean.TYPE	  	|
|-------------	|-------------	|
|char.class		|Character.TYPE	|
|byte.class		|Byte.TYPE		|
|short.class		|Short.TYPE		|
|int.class		|Integer.TYPE		|
|long.class		|Long.TYPE		|
|float.class		|Float.TYPE		|
|double.class		|Double.TYPE		|
|void.class		|Void.TYPE		|

**泛化的Class引用：**  
Class引用总是指向某个Class对象，它可以制造类的实例，并包含可作用于这些实例的所有方法代码。Class引用表示的就是它所指向的对象的确切类型，而该对象就是Class类的一个对象（Class是所有类的类）。  
java5 以后泛型的引入使得Class所表示的类型更加明确

```java
	Class intClass=int.class;//编译器会有警告  
	Class<Integer> genericIntClass=int.class;  
	intClass=double.class;//可以编译通过  
	genericIntClass=double.class;//编译时报错
```
*通过泛型可以让编译器强制执行额外的类型检查。*

## 反射
Class类和java.lang.reflect类库一起对泛型的概念进行了支持，该类库包含了Field，Method以及Constructor（每个类都实现了Member接口）。这些类型的对象是由JVM在运行时创建的，用以表示未知类里对应的成员。  
RTTI和反射之间之间真正的区别在于：  
对于RTTI来说，编译器在编译时打开和检查class文件而对于反射来说，class文件在编译时是不可获取的，所以是在运行时打开和检查class文件。