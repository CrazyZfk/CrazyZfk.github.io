---
layout: post
title:  "hashcode in java"
date:   2017-04-08 10:45:10 +0800
categories: java
---
## 前言
最近在知乎上看到一个关于hashcode的问答（[问答地址](https://www.zhihu.com/question/57356225/answer/152881868)），让我了解了java对于hashcode的处理并没有我想的那么简单。以前一直以为对象hashcode计算的结果是指向对象的引用，如果是这种情况那么至少有下面两种情况不满足：    

* 现在计算机基本都是64位的了，从hashcode的源码可以看到实际hashcode函数返回的是int，int才32位。    
* jvm在GC时需要移动对象这种移动会导致对象在堆中的地址改变，如果每次GC都要重新计算对象的hashcode，那么GC的工作就不是那么存粹了出现了JVM操作用户代码的情况。

### hashcode到底是什么
根据[oracle关于hashcode的文档说明](https://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode())，hashcode的生成有下面三个要求：

* 在程序的一次执行过程中无论何时调用类的hashcode方法都要返回同样的int值，但是并不要求不同的程序执行过程中返回同样的值。
* 如果两个类使用equals方法比较相等，则他们的hashcode返回的int值必须要相同。
* 两个不同的类在equals方法比较是不同的，但是并不要求他们的hashcode方法一定要返回不同的int值。不同的对象的hashcode返回不同的int值能够有效的提高hash表的执行效率。

从上面的要求中我们不难看出，java并没有强制要求对hashcode的实现方法，只是提供了约束条件。文档中对于hashcode的说明中有一句话提到了对象的地址：  
>As much as is reasonably practical, the hashCode method defined by class Object does return distinct integers for distinct objects. (This is typically implemented by converting the internal address of the object into an integer, but this implementation technique is not required by the JavaTM programming language.)


由此可以看出java并没有强制要求使用地址作为hashcode的返回值。

### hashcode的默认实现
你可以通过*System.identityHashCode(o)*来获取o的hashcode，identity hash code会调用hashcode方法，无论hashcode是否被重写。  
hashcode方法是native的，所以hashcode的默认实现取决于使用的JVM，目前使用最多的是hotspot虚拟机，所以一下内容均在虚拟机是hotspot的前提下进行。  
hotspot默认有以下几种实现方式：

0. A randomly generated number.
1. A function of memory address of the object.
2. A hardcoded 1 (used for sensitivity testing.)
3. A sequence.
4. The memory address of the object, cast to int.
5. Thread state combined with [xorshift](https://en.wikipedia.org/wiki/Xorshift)

openJDK6和openJDK7使用第一种方式。   
openJDK8使用第四种方式。  
所以hashcode的实现和地址是没有关系的。  
本节主要基于[点击这里](https://srvaroa.github.io/jvm/java/openjdk/biased-locking/2017/01/30/hashCode.html)

### 后记
openJDK中生成hashcode值的代码如下：

```java
	685   mark = monitor->header();  
	...   
	687   hash = mark->hash();    
	688   if (hash == 0) {  
	689     hash = get_next_hash(Self, obj);  
	...  
	701   } 
	...  
	703   return hash;  
```
由代码可以看出类的hashcode只会被计算一次，一旦计算好了就不会再有变动。
	

  


