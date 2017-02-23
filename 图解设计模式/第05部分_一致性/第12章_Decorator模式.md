# Decorator模式 装饰物模式

现在有一块蛋糕，涂上奶油，就是奶油蛋糕，如果再加上草莓，就是草莓奶油蛋糕，如果再放一个黑巧克力板，用白巧克力写上姓名，插上蜡烛，就成了生日蛋糕。不论是奶油蛋糕，还是草莓奶油蛋糕，还是生日蛋糕，它们的核心还是蛋糕。通过增加装饰物，蛋糕变得美味，目的变得明确。

对象通过增加功能，就变成了目的更加明确的对象。

Decorator模式，就是这样不断的为对象添加装饰的设计模式。

## 示例程序
* Display类
	
	```java
	//Display.java
	public abstract class Display {
		public abstract int getColumns();
		public abstract int getRows();
		public abstract String getRowText(int row);
		public final void show() {
			for(int i=0; i < getRows(); i++){
				System.out.println(getRowText(i));
			}
		}
	}
	```
	
* StringDisplay类
	
	```java
	//StringDisplay.java
	public class StringDisplay extends Display {
		private Stirng string;
		public StringDisplay(String string){
			this.string = string;
		}
		public int getColumns() {
			return string.getBytes().length;
		}
		public int getRows() {
			return 1;
		}
		public String getRowText(int row){
			if (row == 0){
				return string;
			} else {
				return null;
			}
		}
	}
	```
	
* Border类
	
	```java
	//Border.java
	public abstract class Border extends Display {
		protected Display display;
		protected Border(Display display){
			this.display = display;
		}
	}
	```
* SideBorder类
	
	```java
	//SideBorder.java
	public class SideBorder extends Border {
		private char borderChar;
		public SideBorder(Display diplay, char ch) {
			super(display);
			this.borderChar = ch;
		}
		
		public int getColumns(){
			return 1 + display.getColumns() + 1;
		}
		
		public int getRows() {
			return display.getRows();
		}
		
		public String getRowText(int row){
			return borderChar + display.getRowText(row) + borderChar;
		}
	}
	```
* FullBorder类
	
	```java
	//FullBorder.java
	public class FullBorder extends Border {
		public FullBorder(Display display) {
			super(display);
		}
		public int getColumns() {
			return 1 + display.getColumns() + 1;
		}
		public int getRows() {
			return 1 + display.getRows() + 1;
		}
		
		public String getRowText(int row) {
			if ( row == 0 ) {
				return "+" + makeLine("-", display.getColumns()) + "+";
			} else if ( row == display.getRow() + 1) {
			 	return "+" + makeLine("-", display.getColumns()) + "+";
			} else {
				return "|" + display.getRowText(row -1) + "|";
			}
		}
		
		private String makeLine(char ch, int count) {
			StringBuffer buf = new StringBuffer();
			for( int i=0; i < count; i++ ){
				buf.append(ch);
			}
			return buf.toString();
		}
	}
	```
	
* Main类
	
	```java
	//Main.java
	public class Main {
		public static void main(String[] args) {
			Display b1 = new StringDisplay("Hello, world.");
			Display b2 = new SideBorder(b1, "#");
			Display b3 = new FullBorder(b2);
			
			b1.show();
			b2.show();
			b3.show();
			
			Display b4 = new SideBorder(
				new FullBorder(
					new Fullborder(
						new SideBorder(
							new FullBorder(
								new StringDisplay("你好，世界")
							),
							'*'
						);
					);
				);
			);
			b4.show();
		}
	}
	```
	Hello, world.         -> b1.show();
	#Hello, world.#		  -> b2.show();
	+-------------+       -> b3.show();
	|Hello, world.|
	+-------------+
	
## 角色
* Component

	增加功能似的核心角色，定义蛋糕的接口，由Display类扮演。
	
* ConcreteComponent

	该角色实现Component角色定义的接口，是具体的蛋糕。由StringDisplay类扮演。
	
* Decorator（装饰物）

	该角色具有与Component角色相同的接口，在内部保存了被装饰对象-Component角色。由Border类扮演。
	
* ConcreteDecorator（具体的装饰物）

	具体的Decorator角色，由SideBorder类和FullBorder类扮演。

## 拓展思路
* 接口（API）的透明性

	在Decorator模式中，装饰边框与被装饰物具有一致性。装饰边框（以及它的子类）与被装饰物具有相同的API，被装饰物的API不会被隐藏。
	
	Decorator模式与Composite模式一样，都具有递归结构，Decorator模式的目的是通过添加装饰物来增加对象的功能。
	
* 在不改变被装饰物的前提下增加功能

	Decorator模式使用**委托**方式，对装饰边框提出的要求，最终会由被装饰物执行，**可以在不修改被装饰物的前提下完成功能的增加**。
	
* 可以动态地增加功能

	使用委托方式，类之间是弱关联关系，可以在运行时进行调整。
	
* 只需要一些装饰物即可添加许多功能

	通过组合装饰边框，可以提供功能复杂的对象。
	
* java.io包与Decorator模式

	java.io包用于处理输入输出
	```java
	//读取文件
	Reader reader = new FileReader("datafile.txt");
	//读取文件放入缓冲区
	Reader reader = new BufferedReader(
		new FileReader("datafile.txt")
	);
	//增加行号
	Reader reader = new LineNumberReader(
		new BufferedReader(
			new FileReader("datafile.txt")
		)
	);
	```
* 导致增加许多很小的类

	Decorator模式的一个缺点是增加了许多功能相似的小类。

## 相关设计模式
* Adapter模式

	Decorator模式可以在不改变被装饰物的接口前提下增加功能。
	
	Aaapter模式用于适配两个不同的接口。
	
* Stragety模式

	Decorator模式可以像改变别装饰物的边框或是为被装饰物添加多重边框那样，来增加类的功能。
	
	Stragety模式通过替换算法来改变类的功能。
	
## 延伸 继承和委托的一致性
* 继承-父类和子类的一致性

	```java
	class Parent {
		...
		void parentMethod() {
		}
	}
	class Child extends Parent {
		...
		void childMethod(){
			...
		}
	}
	Parent obj = new Child();
	obj.parentMethod());
	
	Parent obj = new Child();
	((Child)obj).childMethod();
	```
* 委托-自己和被委托对象的一致性

	```java
	class Rose {
		Violet obj = ...
		void method() {
			obj.method();
		}
	}
	class Violet {
		void method() {
		...
		}
	}
	//接口也可以
	abstract class Flower {
		abstract void method();
	}
	class Rose extends Flower {
		Violet obj = ...
		void method() {
			obj.method();
		}
	}
	class Violet extends Flower {
		void method() {
			...
		}
	}
	
	```