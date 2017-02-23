# Bridge模式 桥接模式

桥接模式连接**类的功能层次结构**和**类的实现层次结构**。

## 类的层次结构的两个作用
* 希望增加新功能时

	有一个 Something类，想在里面增加一个新的方法时，可以派生一个子类SomethingGood类，
	Something
		-> SomethingGood
	这样就生成了一个层次结构，这个层次结构就是为了增加新功能而产生的层次结构，即**类的功能层次结构**。
	
	* 父类具有基本功能
	* 在子类中增加新的功能
	
	**通常来说，类的层次结构关系不应当过深。**
	
* 希望增加新实现时
	
	在Template Method模式中，父类通过声明抽象方法的方式定义接口，子类实现抽象方法。
	
	通过这种分工，创建出具有高可替换性的类。
	
	AbstractClass
		-> ConcreteClass
		
	这个层次结构不是为了增加功能，而是实现：
	* 父类通过声明抽象方法来定义接口
	* 子类通过实现具体方法来实现接口
	
	这种层次结构就是**类的实现层次结构**。
	
* 类的层次结构的混杂与分离
	
	“类的功能层次结构” 和 “类的实现层次结构” **分离为两个独立的类层次结构**

## 示例程序
### 类的功能层次结构 Display类

```java
	//Display.java
	public class Display {
		private DisplayImpl impl;
		
		public Display(DisplayImpl impl) {
			this.impl = impl;
		}
		
		public void open() {
			impl.rawOpen();
		}
		
		public void print() {
			impl.rawPrint();
		}
		
		public void close() {
			impl.rawClose();
		}
		
		public final void display() {
			open();
			print();
			close();
		}
	}
```
	
### 类的功能层次结构 CountDisplay类

```java
	public class Countdisplay extends Display {
		public CountDisplay(DisplayImpl impl) {
			super(impl);
		}
		
		public void multiDisplay(int times) {
			open();
			for(int i=0; i < times; i++){
				print();
			}
			close();
		}
	}
```
	
### 类的实现层次结构 DisplayImpl类

```java
	public abstract class DisplayImpl {
		public abstract void rawOpen();
		public abstract void rawPrint();
		public abstract void rawClose();
	}
```
	
### 类的实现层次结构 StringDisplayImpl类

```java
	public class StringDisplayImpl extends DisplayImpl {
		private String string;
		private int width;
		
		public StringDisplayImpl(String string){
			this.string = string;
			this.width = string.getBytes().length;
		}
		
		public void rawOpen() {
			printLine();
		}
		
		public void rawPrint() {
			System.out.println("|" + string + "|");
		}
		
		public void rawClose() {
			printLine();
		}
		
		private void printLine() {
			System.out.print("+");
			for(int i=0; i < width; i++){
				System.out.print("-");
			}
			System.out.println("+");
		}
	}
```
	
### Main类

```java
	public class Main {
		public static void main(String[] args){
			Display d1 = new Display( new StringDisplayImpl("Hello, China."));
			Display d2 = new Display( new StringDisplayImpl("Hello, World."));
			CountDisplay d3 = new CountDisplay( new StringDisplayImpl("Hello, Universe."));
			
			d1.display();
			d2.display();
			d3.display();
			d3.multiDisplay(5);
		}
	}
```
	
	输出：
	+-------------+
	|Hello, China.|
	+-------------+
	+-------------+
	|Hello, World.|
	+-------------+
	+----------------+
	|Hello, Universe.|
	+----------------+
	+----------------+
	|Hello, Universe.|
	|Hello, Universe.|
	|Hello, Universe.|
	|Hello, Universe.|
	|Hello, Universe.|
	+----------------+

## Bridge模式中的角色
* Abstraction（抽象化）

	位于“类的功能层次结构”最上层。使用Implementor角色的方法定义了基本功能。该角色保存了Implementor角色的实例。由Display类扮演。
	
* RefinedAbstraction（改善后的抽象化）

	在Abstraction角色的基础上增加了新功能的角色。由CountDisplay类扮演。
	
* Implementor（实现者）

	该角色位于”类的实现层次结构”最上层，定义用于实现Abstraction角色的接口的方法。由DisplayImpl类扮演。
	
* ConcreteImplementor（具体实现者）

	该角色负责实现在Implementor角色总定义的接口，由StringDisplayImpl类扮演。

## 扩展思路
* 分开后更容易扩展

	Bridge模式的特征是将“类的功能层次结构”与“类的实现层次结构”分离开了。这种分离有利于独立的对它们进行扩展。
	
	当想增加新功能时，只需要在“类的功能层次结构”一侧增加类即可，**增加后的功能可以被“所有的实现”使用。**
	
	可以将“类的功能层次结构”应用于软件所运行的操作系统上。将依赖操作系统的部分划分为Windows版、Mac版、Linux版，采用“类的实现层次结构”来表现这些依赖操作系统的部分。
	
	编写Implementor角色定义这些操作系统的共同接口。编写Windows版、Mac版、Linux版3个ConcreteImplementor角色。
	
	这样，无论在“类的功能层次结构”中增加多少功能，都可以工作于这3个操作系统。
	
* 继承是强关联，委托是弱关联

	通过“继承”很容易完成类的功能扩展，同时类之间也建立起了强关联，这种关联如果不修改代码就无法改变。
	
	class SomethingGood extends Something {
		...
	}
	
	Display类中使用了“委托”，在impl字段总保存了实现的实例：
	* 调用open方法会调用impl.rawOpen()方法
	* 调用print方法会调用impl.rawPrint()方法
	* 调用close方法会调用impl.rawClose()方法
	
	当其他类要求Display类“工作”时，Display类会委托impl中保存的实例进行。
	
	Main类中，Kay在运行时，将StringDisplayImpl类的实例传入，完成替换升级。

## 相关设计模式
* Template Method模式

	在Template Method模式中使用了“类的实现层次结构”。父类调用抽象方法，子类实现抽象方法。
	
* Abstract Factory模式

	使用Abstract Factory模式可以设计出良好的ConcreteImplementor角色。
	
* Adapter模式

	Bridge模式可以达到类的功能层次结构与类的实现层次结构分离的目的，并在此基础上使这些层次结构结合起来。
	
	Adapter模式用来结合那些功能上相似但是接口不同的类。

