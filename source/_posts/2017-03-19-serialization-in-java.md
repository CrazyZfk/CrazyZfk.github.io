---
layout: post
title:  "serialization in java"
date:   2017-04-16 10:45:10 +0800
categories: java
---
## 前言
java序列化是java作为网络编程语言的一个重要特性，序列化使得java对象可以被转为字节码，用于对象的存储和传输。
一个对象能被序列化就要实现java.io.Serializable：

```java
 * @author  unascribed
 * @see java.io.ObjectOutputStream
 * @see java.io.ObjectInputStream
 * @see java.io.ObjectOutput
 * @see java.io.ObjectInput
 * @see java.io.Externalizable
 * @since   JDK1.1
 */
public interface Serializable {
}
```
该接口是一个空接口，只是用于标记如果没有实现该接口就不能序列化，其中writeObject方法会检测传入的类是否实现了java.io.Serializable接口，如果类没有实现java.io.Serializable接口序列号时会导致报错。

## 实现java.io.Serializable
假设有这样一个类没有实现java.io.Serializable：

```java
class Student {
	private String name;
	private int age;
	private int grade;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getGrade() {
		return grade;
	}
	public void setGrade(int grade) {
		this.grade = grade;
	}
}
```
对该类调用writeObject：

```java
public class SerializationTest {
	public static void main(String[] args) {
		try {
			ByteArrayOutputStream baos=new ByteArrayOutputStream();
			ObjectOutputStream oos=new ObjectOutputStream(baos);
			Student student=new Student();
			student.setName("name");
			student.setAge(10);
			student.setGrade(6);
			oos.writeObject(student);
			ObjectInputStream ois=new ObjectInputStream(new ByteArrayInputStream(baos.toByteArray()));
			Student student1=(Student) ois.readObject();
			System.out.println(student1.getName()+","+student1.getAge()+","+student1.getGrade());
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch(ClassNotFoundException e){
			e.printStackTrace();
		}

	}

}
```
执行结果如下：

```java
java.io.NotSerializableException: serialization.Student
	at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1183)
	at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:347)
	at serialization.SerializationTest.main(SerializationTest.java:20)
```
writeObject方法中会验证传入的参数是否实现了java.io.Serializable：

```java
            if (obj instanceof String) {
                writeString((String) obj, unshared);
            } else if (cl.isArray()) {
                writeArray(obj, desc, unshared);
            } else if (obj instanceof Enum) {
                writeEnum((Enum) obj, desc, unshared);
            } else if (obj instanceof Serializable) {
                writeOrdinaryObject(obj, desc, unshared);
            } else {
                if (extendedDebugInfo) {
                    throw new NotSerializableException(
                        cl.getName() + "\n" + debugInfoStack.toString());
                } else {
                    throw new NotSerializableException(cl.getName());
                }
            }

```

## serialVersionUID
序列化对象在被反序列化为类的时候会检验serialVersionUID，如果没有手动指定该值会在序列化时根据类的信息生成一个。序列化时由编译器生成的serialVersionUID会高度依赖类的细节，而且和编译器的实现有很大的关系，也就是说可能存在同样的类在不同的编译器中生成不同的serialVersionUID的情况，或者同样的类仅仅因为改变了类的细节就会导致产生的serialVersionUID不同。  
### 这种不同会有什么影响？
我们先把上个例子中的student写入到文件中:

```java
FileOutputStream fos=new FileOutputStream("student.ser");
ObjectOutputStream oos=new ObjectOutputStream(fos);
Student student=new Student();
student.setName("name");
student.setAge(10);
student.setGrade(10);
oos.writeObject(student);
```
在将student修改为：

```java
class Student implements Serializable{
	private String name;
	private int age;
	private int grade;
	private String school;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getGrade() {
		return grade;
	}
	public void setGrade(int grade) {
		this.grade = grade;
	}
	public String getSchool() {
		return school;
	}
	public void setSchool(String school) {
		this.school = school;
	}
	
}

```
在student类中添加了一个字段school，然后在从文件中读取：

```java
FileInputStream fis=new FileInputStream("student.ser");  
ObjectInputStream ois=new ObjectInputStream(fis);  
Student student1=(Student) ois.readObject();  
System.out.println(student1.getName()+","+student1.getAge()+","+student1.getGrade());  
ois.close();
fis.close();
```
就会出现如下错误

```java
java.io.InvalidClassException: serialization.Student; local class incompatible: stream classdesc serialVersionUID = -1205959179532190345, local class serialVersionUID = -3693389777067287057
	at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:617)
	at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1622)
	at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1517)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1771)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1350)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:370)
	at serialization.SerializationTest.main(SerializationTest.java:27)
```
其中错误很清晰的告诉你错误原因是本地的类和读取的类的serialVersionUID不等，这是我们指定新student类的serialVersionUID值为-1205959179532190345

```java
private static final long serialVersionUID = -1205959179532190345L;
```
再次从文件中读取就没有问题。  
**因此在序列化类时指定类的serialVersionUID值对于类是很有必要的**
### serialVersionUID的主要作用
serialVersionUID主要的目的是记录类的不同版本，使得类被反序列化时序列成正确的版本。指定类的serialVersionUID也可以有效的避免不同JVM实现导致的反序列化失败。

## 序列化字段
有时我们不需要序列化全字段，只需要将类中的指定部分字段序列化。首先我们要明确的是类中的静态变量是不会序列化的，因为静态字段是属于类的，序列化是针对类的实例的。
### 使用Externalizable
java.io.Externalizable是java.io.Serializable的子接口，该接口有两个方法

```java
void writeExternal(ObjectOutput out) throws IOException;
void readExternal(ObjectInput in) throws IOException, ClassNotFoundException;
```
在这两个方法中可以指定那些字段序列化（[点击了解更多](http://www.cnblogs.com/chenfei0801/archive/2013/04/06/3002146.html)）。  
**实现这个接口需要类的存在默认的具有public访问权限的构造函数**
### transient
被transient修饰的字段是不会被序列化的。例如我们将student类修改为：

```java

class Student implements Serializable{
	/**
	 * 
	 */
	private static final long serialVersionUID = -1205959179532190345L;
	private String name;
	private int age;
	private int grade;
	private transient String school;
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public int getAge() {
		return age;
	}
	public void setAge(int age) {
		this.age = age;
	}
	public int getGrade() {
		return grade;
	}
	public void setGrade(int grade) {
		this.grade = grade;
	}
	public String getSchool() {
		return school;
	}
	public void setSchool(String school) {
		this.school = school;
	}
	
}
```
使用transient修饰school，然后我们序列化后再读取：

```java
public class SerializationTest {

	public static void main(String[] args) {
		try {
			//ByteArrayOutputStream baos=new ByteArrayOutputStream();
			FileOutputStream fos=new FileOutputStream("student.ser");
			ObjectOutputStream oos=new ObjectOutputStream(fos);
			Student student=new Student();
			student.setName("name");
			student.setAge(10);
			student.setGrade(6);
			oos.writeObject(student);
			FileInputStream fis=new FileInputStream("student.ser");
			//ObjectInputStream ois=new ObjectInputStream(new ByteArrayInputStream(baos.toByteArray()));
			ObjectInputStream ois=new ObjectInputStream(fis);
			Student student1=(Student) ois.readObject();
			System.out.println(student1.getName()+","+student1.getAge()+","+student1.getGrade()+","+student1.getSchool());
			ois.close();
			fis.close();
			oos.close();
			fos.close();
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch(ClassNotFoundException e){
			e.printStackTrace();
		}

	}

```
执行的结果如下：

```java
name,10,6,null
```
由此可见school并没有被序列化，所以反序列化的值为null。
