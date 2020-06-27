---
layout: post
title: "for-vs-foreach-in-java"
date: 2018-03-19 20:30:23 +0800
comments: true
categories: "java"
---
## 前言
一直觉得在java中使用foreach会带来一定的效率的提升，今天调研才发现并没有，看来事情还是要多调研，不能直视凭直觉来断定事情。

## 举例
如下两个例子：

```java
for(Object o: objectArrayList){
    o.DoSomthing();
}
```
and

```java
for(int i=0; i<objectArrayList.size(); i++){
    objectArrayList.get(i).DoSomthing();
}
```
根据我查到的资料显示，其实他们两个编译后的代码时没有区别的，foreach只是比for看起来更加简洁，唯一说可能有性能差异的地方也就是for会每次计算列表的长度。  
不信你自己看[这里](http://stackoverflow.com/questions/256859/is-there-a-performance-difference-between-a-for-loop-and-a-for-each-loop)