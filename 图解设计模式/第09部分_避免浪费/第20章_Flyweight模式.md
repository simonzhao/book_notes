# Flyweight模式 轻量级模式

通过尽量共享实例来避免new出实例。

## 示例程序

0

................
....######......
..##......##....
..##......##....
..##......##....
..##......##....
..##......##....
....######......
................



................
......##........
..######........
......##........
......##........
......##........
......##........
..##########....
................

### BigChar类

```Java
//BigChar.java

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class BigChar {
	private char channame;
	private String fontdata;
	
	public BigChar(char charname){
		this.charname = charname;
		try {
			Bufferedreader reader = new BufferedReader( new FileReader("big"+charname+".txt") );
			String line;
			StringBuffer buf = new StringBuffer();
			while((line = reader.readLine()) != null ){
				buf.append(line);
				buf.append("\n");;
			}
			reader.close();
			this.fontdata = buf.toString();
		} catch(IOException e) {
			this.fontdata = charname + "?";
		}
	}
	public void print() {
		System.out.print(fontdata);
	}
}
```

### BigCharFactory类

```Java
//BigCharFactory.java

import java.util.HashMap;

public class BigCharFactory {
	private HashMap pool = new HashMap();
	private static BigCharFactory singleton = new BigCharFactory();
	private BigCharFactory() {
	}
	
	public static BigCharFactory getInstance() {
		return singleton;
	}
	
	public synchronized BigChar getBigChar(char charname) {
		BigChar bc = (BigChar)pool.get("" + charname);
		if (bc == null){
			bc = new BigChar(charname);
			pool.put("" + charname, bc);
		}
		return bc;
	}
}
```

### BigString类
```Java
//BigString.java
public class BigString {
	private BigChar[] bigchars;
	public BigString(String string) {
		bigchars = new BigChar[string.length()];
		BigCharFactory factory = BigCharFactory.getInstance();
		for(int i=0; i < bigchars.length; i++) {
			bigchars[i] = factory.getBigchar(string.charAt(i));
		}
	}
	
	public void print() {
		for(int i=0; i < bigchars.length; i++) { 
			bigchars[i].print();
		}
	}
}
```
### Main类
```Java
//Main.java
public class Main {
	public static void main(String[] args){
		if ( args.length == 0){
			System.out.println("Usage: java Main digits");
			System.out.println("Example: java Main 1212123");
			System.exit(0);
		}
		
		BigString bs = new BigString(args[0]);
		bs.print();
	}
}
```

## 角色
* Flyweight（轻量级）

	Flyweight角色就是那些实例被共享的类。
	
* FlyweightFactory（轻量级工厂）
	
	FlyweightFactory角色是生成Flyweight角色的工厂。
	
* Client（请求者）

	Client角色使用FlyweightFactory角色来生成Flyweight角色。

## 拓展思路的要点
### 对多个地方产生影响

	如果改变被共享的对象，就会对多个地方产生影响。
	
### Intrinsic与Extrinsic

	应当共享的信息被称作Intrinsic信息，本质的，固有的。
	
	不应当共享的信息被称作Extrinsic信息，外在的，非本质的。
	
### 不要让被共享的实例被垃圾回收器回收了

	如果一个实例有相关引用的话就不会被回收，示例中，将实例放入了pool容器中。如果移除后，将会被回收。
	
### 内存之外的其他资源

	通过共享引用，可以减少创建实例的系统开销。
	
	
## 相关的设计模式
* Proxy模式
	
	使用Flyweight模式可以提高程序生成实例的速度。
	
	Proxy模式通过设置代理提高程序的处理速度。
	
* Composite模式

	有时通过Flyweight模式共享Composite模式中的Leaf角色。
	
* Singleton模式

	在FlyweightFactory角色有时会使用Singleton模式。
