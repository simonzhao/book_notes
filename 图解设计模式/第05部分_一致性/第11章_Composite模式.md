# Composite模式 复合物模式

文件系统中存在文件夹和文件，文件夹中可以放入文件也开可以放入子文件夹。
文件夹和文件可以统称为“目录条目”。

容器和内容可以作为同一种东西看待，容器中可以放入内容，也可以放入小容器，小容器中也可以放入内容和更小的容器。这样就是形成了一种递归结构。

Composite模式就是**能够使容器与内容具有一致性，创造出递归结构**的模式。

## 示例程序
* Entry类

	```java
	//Entry.java
	public abstract class Entry {
		public abstract String getName();
		public abstract int getSize();
		public Entry add(Entry entry) throws FileTreatmentException {
			throw new FileTreatmentException();
		}
		public void printList(){
			printList("");
		}
		protected abstract void printList(String prefix);
		public String toString() {
			return getName() + " (" + getSize() + ")";
		}
	}
	```
* File类
	
	```java
	//File.java
	public class File extends Entry {
		private String name;
		private int size;
		public File(String name, int size) {
			this.name = name;
			this.size = size;
		}
		
		public String getName(){
			return name;
		}
		
		public int getsize(){
			return size;
		}
		
		protected void printList(String prefix){
			System.out.println(prefix + "/" + this);
		}
	}
	```
	
* Directory类
	
	```java
	//Directory.java
	import java.util.Iterator;
	import java.util.ArrayList;
	
	public class Directory extends Entry {
		private String name;
		private ArrayList directory = new ArrayList();
		
		public Directory(String name){
			this.name = name;
		}
		
		public String getName(){
			return name;
		}
		
		public int getSize() {
			int size = 0;
			Iterator it = directory.iterator();
			while(it.hasNext()){
				Entry entry = (Entry)it.next();
				size += entry.getSize();
			}
			retrun size;
		}
		
		public Entry add (Entry entry){
			directory.add(entry);
			return this;
		}
		
		protected void printList(Stirng prefix) {
			System.out.println(prefix + "/" + this);
			Iterator it = directory.iterator();
			while(it.hasNext()){
				Entry entry = (Entry)it.next();
				entry.printList(prefix + "/" + name);
			}
		}
	}
	```
	
* FileTreatMentException类
	
	```java
	//FileTreatMentException.java
	public class FileTreatmentException extends RuntimeException {
		public FileTreatmentException () {
		}
		
		public FileTreatmentException(String msg) {
			super(msg);
		}
	}
	```
	
* Main类
	
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
				
				bindir.add( new File("vi", 10000) );
				bindir.add( new File("latex", 20000) );
				
				rootdir.printList();
				
				System.out.println("");
				System.out.println("Making user entries...");
				
				Directory yuki = new Directory("yuki");
				Directory hanako = new Directory("hanako");
				Directory tomura = new Directory("tomura");
				
				usrdir.add(yuki);
				usrdir.add(hanako);
				usrdir.add(tomura);
				
				yuki.add( new File("diary.html", 100) );
				yuki.add( new File("Composite.java", 200) );
				hanako.add( new File("memo.tex", 300) );
				tomura.add( new File("game.doc", 400) );
				tomura.add( new File("junk.mail", 500) );
				
				rootdir.printList();
			} catch ( FileTreatmentException e) {
				e.printStackTrace();
			}
		}
	}
	```

## 角色
* Leaf（树叶）

	表示“内容”的角色，该角色不能放入其他对象，File类扮演。
	
* Composite（复合物）

	表示“容器”的角色，在其中可以放入Leaf角色和Composite角色，由Directory扮演。
	
* Component

	使Leaf角色和Composite角色具有一直性的角色，Component角色是Leaf角色和Composite角色的父类，由Entry类扮演。
	
* Client

	使用Composite模式的角色，由Main类扮演。

## 拓展思路
* 多个和单个的一致性

	使用Composite模式可以使容器与内容具有一致性，也可以称为**多个和单个的一致性**，即将多个对象结合在一起，当作一个对象进行处理。
	
	* Test1是输入数据来自键盘。
	* Test2是输入数据来自文件。
	* Test3是输入数据来自网络。
	
	可以采用Composite模式将他们统一为“输入测试”。
	
* Add方法应该放在哪里？
	* 方法1：定义在Entry类中，报错
	
		示例程序中的做法，Directory类重写add方法，实现需求。File类通过继承可以调用add方法，但是会报错。
		
	* 方法2：定义在Entry类中，但什么也不做
	
		File类调用add方法，不会报错。
		
	* 方法3：声明在Entry类中，但不实现
	
		对Directory类没有影响，File类必须实现一下自己不关心的方法。
		
	* 方法4：只定义在Directory类中
	
		要向Entry类型的变量进行add时，必须转换为Directory类。
		
* 到处都存在递归结构

	树结构的数据结构都适用Composite模式。

## 相关设计模式
* Command模式

	使用Command模式编写宏命令时使用Composite模式。
	
* Visitor模式

	可以使用Visitor模式访问Composite模式中的递归结构。
	
* Decorator模式

	Composite模式通过Component角色使容器（Composite角色）和内容（Leaf角色）具有一致性。
	
	Decorator模式使装饰框和内容具有一致性。
	

