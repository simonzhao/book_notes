# Proxy模式  代理人模式

只在必要时生成实例

## 示例程序
### Printer类
```Java
//Printer.java

public class Printer implements Printable {
	private String name;
	public Printer() {
		heavyJob("正在生成Printer的实例");
	}
	
	public Printer(String name) {
		this.name = name;
		heavyJob("正在生成Printer的实例（" + name + "）");
	}
	
	public void setPrinterName(String name){
		this.name = name;
	}
	
	public String getPrinterName(){
		return name;
	}
	
	public void print(String string){
		System.out.println("===" + name + "===");
		System.out.println(string);
	}
	
	public void heavyJob(String msg) {
		Sysem.out.print(msg);
		for(int i=0; i < 5; i++){
			try{
				Thread.sleep( 1000 );
			} catch(InterruptedException e) {
				
			}
			System.out.print(".");
		}
		System.out.println("结束");
	}
}
```

### Printable接口

```Java
//Printable.java

public interface Printable {
	public abstract void setPrinterName(String name);
	public abstract String getPrinterName();
	public abstract void print(String string);
}
```

### PrinterProxy类

```Java
public class PrinterProxy implements Printable {
	private String name;
	private Printer real;
	public PrinterProxy {
	}
	
	public PrinterProxy(String name){
		this.name = name;
	}
	
	public synchronized void setPrinterName(String name) {
		if(real != null){
			real.setPrinterName(name);
		}
		this.name = name;
	}
	
	public String getPrinterName() {
		return name;
	}
	
	public void print(String string) {
		realize();
		real.print(string);
	}
	
	private synchronized void realize() {
		if ( real == null){
			real = new Printer(name);
		}
	}
}
```
### Main类
```Java
public class Main{
	public static void main(String[] args){
		Printable p = new PrinterProxy("Alice");
		System.out.println("现在的名字是" + p.getPrinterName() + "。");
		p.setPrinterName("Bob");
		System.out.println("现在的名字是" + p.getPrinterName() + "。");
		p.print("Hello, world");
	}
}
```

## 角色
* Subject（主体）

	Subject角色定义了使Proxy角色和RealSubject角色之间具有一致性的接口。由于存在Subject角色，所以Client角色不必在意它使用的是Proxy角色还是RealSubject角色。
	
* Proxy（代理人）

	Proxy角色会尽量处理来自Client角色的请求，在必要时在生成RealSubject角色。
	
* RealSubject（实际的主体）

	“本人”RealSubject角色会在Proxy角色不能胜任时出场，实际完成工作。
	
* Client（请求者）
	
	使用Proxy模式的角色。

## 拓展思路的要点
### 使用代理人来提升处理速度

	所以代理模式，将创建实例的慢动作延后到实际运行时。例如图片懒加载。
	
### 有必要划分代理人和本人吗？

	也可以不划分PrinterProxy类和Printer类，直接在Printer类中进行惰性求值功能。
	
	通过划分PrinterProxy角色好Printer角色，使它们成为独立的组件，避免在修改是产生相互影响。
	
### 代理与委托

	代理人委托本人执行具体功能。
	
### 透明性

	PrinterProxy类和Printer类都实现了Printable接口，因此Main类可以不在意调用的是谁。这种就是透明性。
	
### HTTP代理

	浏览器扮演Client角色， HTTP代理扮演Proxy角色， Web服务器扮演RealSubject角色。
	
### 各种Proxy模式

* Virtual Proxy（虚拟代理）

	当真正需要时才生成实例。
	
* Remote Proxy（远程代理）

	RealSubject角色可以部署在本地也可以在远程。Java的RMI（Remote MethodInvocation）就相当于Remote Proxy。
	
* Access Proxy

	在调用RealSubject角色的功能时设置访问限制。

## 相关的设计模式
* Adapter模式

	Adapter模式适配两种具有不同接口的对象。 Proxy角色与realSubject角色的接口是相同的。
	
* Decorator模式

	Decorator模式的目的在于增加新功能。
	
	Proxy模式更注重通过设置代理人来减轻本人的负担。
