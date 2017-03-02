# Visitor模式 访问者模式

访问数据结构并处理数据

在Visitor模式中，**数据结构与处理被分离开**。

## 示例程序
### Visitor类

	```java
	
	//Visitor.java
	public abstract class Visitor {
		public abstract void visit(File file);
		public abstract void visit(Directory directory);
	}
	
	```
	

	```java
	//Visitor.java
	public abstract class Visitor {
		public abstract void visit(File file);
		public abstract void visit(Directory directory);
	}
	```
### Element接口
	```java
	//Element.java
	public interface Element {
		public abstract void accept( Visitor v );
	}
	```
### Entry类
	```java
	//Entry.java
	import java.util.Iterator;
	
	public abstract class Entry implements Element {
		public abstract String getName();
		public abstract int getSize();
		public Entry add(Entry entry) throws FileTreatmentException {
			throw new FileTreatmentException();
		}
		
		public Iterator iterator() throws FileTreatmentException {
			throw new FileTreatmentException();
		}
		
		public String toString() {
			return getName() + "(" + getSize() + ")";
		}
	}
	```
### File类
	```java
	//File.java
	public class File extends Entry {
		private String name;
		private int size;
		public File(String name, int size) {
			this.name = name;
			this.size = size;
		}
		public String getName() {
			return name;
		}
		public int getSize() {
			return size;
		}
		public void accept( Visitor v ) {
			v.visit(this);
		}
	}
	```
### Directory类
	```java
	//Directory.java
	import java.util.Iterator;
	import java.util.ArrayList;
	public class Directory extends Entry {
		private String name;
		private ArrayList dir = new ArrayList();
		
		public Directory(String name) {
			this.name = name;
		}
		public String getName() {
			return name;
		}
		public int getSize() {
			int size = 0;
			Iterator it = dir.iterator();
			while(it.hasNext()){
				Entry entry = (Entry)it.next();
				size += entry.getSize();
			}
			return size;
		}
		public Entry add(Entry entry){
			dir.add(entry);
			return this;
		}
		public Iterator iterator() {
			return dir.iterator();
		}
		public void accept(Visitor v){
			v.visit(this);
		}
	}
	```
### ListVisitor类
	```java
	//ListVisitor.java
	import java.util.Iterator;
	public class ListVisitor extends Visitor {
		private String currentdir = "";
		public void visit(File file){
			System.out.println(currentdir + "/" + file);
		}
		public void visit(Directory directory) {
			System.out.println(currentdir + "/" + directory);
			String savedir = currentdir;
			currentdir = currentdir + "/" + directory.getName();
			Iterator it = directory.iterator();
			while(it.hasNext()){
				Entry entry = (Entry)it.next();
				entry.accept( this );
			}
			currentdir = savedir;
		}
	}
	```
### FileTreatmentException类
	```java
	public class FileTreatmentException extends RuntimeException {
		public FileTreatmentException {
		}
		public FileTreatmentException(String msg){
			super(msg);
		}
	}
	```
### Main类
		
		```java
		//Main.java
		public class Main {
			public static void main(String[] args){
				try {
					System.out.println("Making root entries...");
					Directory rootdir = new Directory("root");
					Directory bindir = new Directory("bin");
					Directory tmpdir = new Directory("tmp");
					Directory usrdir = new Directory("usr");
				
					rootdir.add(bindir);
					rootdir.add(tmpdir);
					rootdir.add(usrdir);
				
					bindir.add( new File("vi", 10000));
					bindir.add( new File("latex", 20000));
				
					rootdir.accept( new ListVisitor() );
				
					System.out.println("");
					System.out.println("Make user entries...");
				
					Directory yuki = new Directory("yuki");
					Directory hanako = new Directory("hanako");
					Directory tomura = new Directory("tomura");
				
					usrdir.add(yuki);
					usrdir.add(hanako);
					usrdir.add(tomura);
				
					yuki.add(new File("diary.html", 100));
					yuki.add(new File("Composite.java", 200));
					hanako.add(new File("memo.tex", 300));
					tomura.add(new File("game.doc", 400));
					tomura.add(new File("junk.mail", 500));
					rootdir.accept( new ListVisitor() );
				} catch (FileTreatmentException e){
					e.printStackTrace()
				}
			}
		}
		```

## 角色
* Visitor（访问者）
	
	Visitor 角色负责对数据结构中每个具体的元素（ConcreteElement角色）声明一个用于访问XXXX的visit(XXXX)方法，这个方法有ConcreteVisitor角色实现这个方法，由Visitor类扮演。
	
* ConcreteVisitor（具体的访问者）
	
	ConcreteVisitor角色实现Visitor角色定义的接口。由ListVisitor类扮演。
	
* Element（元素）

	Element角色表示Visitor角色的访问对象，声明接受访问者的accept方法。
	
	accept方法接收的参数是Visitor角色。
	
	由Element接口扮演。
	
* ConcreteElement（具体的元素）

	ConcreteElement角色负责实现Element角色所定义的接口，由File类和Directory类扮演。
	
* ObjectStructure（对象结构）

	ObjectStructure角色负责处理Element角色的集合。ConcreteVisitor角色为每个Element角色都准备了处理方法，由Directory类扮演，提供了iterator方法使得 ConcreteVisitor 角色可以遍历处理每个Element角色。

## 拓展思路
### 双重分发
	
	accept方法的调用方式如下：
	
	```java
	element.accept(visitor);
	```
	
	visit方法的调用方式如下：
	
	```java
	visitor.visit(element);
	```
	
	element接受了Visitor， 而 visitor 又访问 element。
	
	在Visitor模式中，ConcreteElement 和 ConcreteVisitor 两个角色共同决定了实际进行的处理。这种消息分发的方式被称为**双重分发（double dispatch）**。
	
### 为什么要弄得这么复杂

	Visitor模式的目的是**将处理从数据结构中分离出来**。 ListVisitor类作为显示文件夹内容的ConcreteVisitor角色，它独立于File类和Directory类。
	从而提高了File类和Directory类的独立性。
	
### 开闭原则——对扩展开放，对修改关闭

* **对扩展（extension）是开放（open）的**
* **对修改（modification）是关闭（close）的**

**在不修改现有代码的前提下进行扩展**


### 易于增加ConcreteVisitor角色

	处理与数据分开，增加ConcreteVisitor角色不会影响现有代码。
	
### 难以增加ConcreteElement角色

	对ConcreteElement角色的增加，将导致所有的ConcreteVisitor都进行修改。
	
### Visitor工作所需的条件

	访问者必须获取足够多的信息才能工作，为此Directory类提供了accept方法和iterator方法。
	如果公开了不应当公开的信息，将对未来数据结构的改良带来影响。

## 相关的设计模式
* Iterator模式

	Iterator模式和Visitor模式都是在某种数据结构上进行处理。
	
	Iterator模式用于逐个遍历保存在数据结构中的元素。
	
	Visitor模式用于对保存在数据结构中的元素进行某种特定的处理。
	
* Composite模式

	有时访问者所访问的数据结构会使用Composite模式。
	
* Interpreter模式

	在Interpreter模式中，有时会使用Visitor模式。
