# Chain of Responsibility模式 职责链

将多个对象组成一条职责链，然后按照它们在职责连上的顺序一个一个的找出到底应该谁来处理。

## 示例程序
### Trouble类

	```java
	//Trouble.java
	/**
	 * 表示发生问题的类
	 */
	public class Trouble {
		private int number; //问题编号
		public Trouble(int number) {
			this.number = number;
		}
		public int getNumber() {
			return number;
		}
		public String toString() {
			return "[Trouble " + number + "]";
		}
	}
	```
### Support类
	```java
	//Support.java
	/**
	 *  用来解决问题的抽象类
	 */
	 public abstract class Support {
	 	private String name;
		private Support next;
		public Support(String name) {
			this.name = name;
		}
		public Support setNext(Support next) {
			this.next = next;
			return next;
		}
		
		public final void support( Trouble trouble ) {
			if ( resolve(trouble) ){
				done(trouble);
			} else if ( next != null ) {
				next.support( trouble );
			} else {
				fail(trouble);
			}
		}
		
		public String toString() {
			return "[" + name + "]";
		}
		
		protected abstract boolean resolve(Trouble trouble);
		
		protected void done(Trouble trouble) {
			System.out.println( trouble + " is resolved by " + this + ".");
		}
		
		protected void fail(Trouble trouble) {
			System.out.println( trouble + " cannot be resolved." );
		}
	 }
	```
### NoSupport类
	```java
	//NoSupport.java
	/**
	 * 不解决问题的类
	 */
	public class NoSupport extends Support{
		public NoSupport(String name) {
			super(name);
		}
		protected boolean resolve(Trouble trouble){
			return false;
		}
	}
	```
### LimitSupport类
	```java
	//LimitSupport.java
	public class LimitSupport extends Support {
		private int limit;
		public LimitSupport(String name, int limit) {
			super(name);
			this.limit = limit;
		}
		protected boolean resolve(Trouble trouble) {
			if(trouble.getNumber() < limit) {
				return true;
			} else {
				return false;
			}
		}
	}
	```
### OddSupport类
	```java
	//OddSupport.java
	public class OddSupport extends Support {
		public OddSupport(String name) {
			super(name);
		}
		protected boolean resolve(Trouble trouble){
			if (trouble.getNumber() % 2 == 1){
				return true;
			} else {
				return false;
			}
		}
	}
	```
### SpecialSupport类
	```java
	public class SpecialSupport extends Support {
		private int number;
		public SpecialSupport(String name, int number) {
			super(name);
			this.number = number;
		}
		protected boolean resolve(Trouble trouble) {
			if(trouble.getNumber() == number) {
				return true;
			} else {
				return false;
			}
		}
	}
	```
### Main类
	```java
	public class Main{
		public static void main(String[] args){
			Support alice = new NoSupport("Alice");
			Support bob = new LimitSupport("Bob", 100);
			Support charlie = new SpecialSupport("Charlie", 429);
			Support diana = new LimitSupport("Diana", 200);
			Support elmo = new OddSupport("Elmo");
			Support fred = new LimitSupport("Fred", 300);
			
			alice.setNext(bob).setNext(charlie).setNext(diana).setNext(elmo).setNext(fred);
			
			for(int i=0; i < 500; i += 33){
				alice.support(new Trouble(i));
			}
		}
	}
	
	[Trouble 0] is resolved by [Bob].
	[Trouble 33] is resolved by [Bob].
	[Trouble 66] is resolved by [Bob].
	[Trouble 99] is resolved by [Bob].
	[Trouble 132] is resolved by [Diana].
	[Trouble 165] is resolved by [Diana].
	[Trouble 198] is resolved by [Diana].
	[Trouble 231] is resolved by [Elmo].
	[Trouble 264] is resolved by [Fred].
	[Trouble 297] is resolved by [Elmo].
	[Trouble 330] cannot be resolved.
	[Trouble 363] is resolved by [Elmo].
	[Trouble 396] cannot be resolved.
	[Trouble 429] is resolved by [Charlie].
	[Trouble 462] cannot be resolved.
	[Trouble 495] is resolved by [Elmo].
	```
	
## 角色
* Handler（处理者）

	Handler角色定义了处理请求的接口，由Support类扮演。
	
* ConcreteHandler（具体的处理者）
	
	具体的处理请求的角色。由NoSupport、LimitSupport等类扮演。
	
* Client（请求者）
	
	由Main类扮演，向职责链发出请求的请求者。

## 拓展思路
### 弱化了发出请求的人和处理请求的人之间的关系

	Chain of Responsibility模式最大的优点是弱化了发出请求的人和处理请求的人之间的关系。
	
### 可以动态的改变职责链
### 专注于自己的工作
### 推卸请求会导致处理延迟

## 相关设计模式
* Composite模式

	Handler角色经常使用Composite模式
	
* Command模式

	有时会使用Command模式向Handler角色发送请求。
