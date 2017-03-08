# Observer模式 观察者模式

当被观察对象的状态发生变化时，会通知给观察者。

## 示例程序

观察者将观察一个生成数值的类，并将它生成的数值结果显示出来，一个直接打印，另一个用简单图形

### Observer接口
```Java
//Observer.java
public interface Observer {
	public abstract void update(NumberGenerator generator);
}
```
### NumberGenerator类
```Java
//NumberGenerator.java
import java.util.ArrayList;
import java.util.Iterator;

public abstract class NumberGenerator {
	private ArrayList observers = new ArrayList(); //保存Observer们
	public void addObserver(Observer observer){
		observers.add(observer);
	}
	public void deleteObserver(Observer observer) {
		observers.remove(observer);
	}
	public void notifyObservers() {
		Iterator it = observers.iterator();
		while(it.hasNext()){
			Observer o = (Observer)it.next();
			o.update(this);
		}
	}
	public abstract int getNumber();
	public abstract void execute();
}
```
### RandomNumberGenerator类
```Java
//RandomNumberGenerator.java
import java.util.Random;
public class RandomNumberGenerator extends NumberGenerator {
	private Random random = new Random();
	private int number;
	public int getNumber() {
		return number;
	}
	public void execute() {
		for(int i=0; i < 20; i++){
			number = random.nextInt(50);
			notifyObservers();
		}
	}
}
```
### DigitObserver类
```Java
//DigitObserver.java
public class DigitObserver implements Observer {
	public void update(NumberGenerator generator) {
		System.out.println("DigitObserver:" + generator.getNumber());
		try {
			Thread.sleep(100);
		} catch(InterruptedException e){
		}
	}
}
```
### GraphObserver类
```Java
//GraphObserver.java
public class GraphObserver implements Observer {
	public void update(NumberGenerator generator) {
		System.out.print("DigitObserver:");
		int count = generator.getNumber();
		for(int i=0; i < count; i++){
			System.out.print("*");
		}
		System.out.println("");
		try {
			Thread.sleep(100);
		} catch(InterruptedException e){
		}
	}
}
```
### Main类
```Java
//Main.java
public class Main {
	public static void main(String[] args){
		NumberGenerator generator = new RandomNumberGenerator();
		Observer observer1 = new DigitObserver();
		Observer observer2 = new GraphObserver();
		generator.addObserver(observer1);
		generator.addObserver(observer2);
		generator.execute();
	}
}
```

## 角色
* Subject（观察对象）
	
	表示被观察对象，定义观察者的注册与移除方法，与获取状态的方法，由NumberGenerator类扮演。
	
* ConcreteSubject（具体的观察对象）

	具体的被观察对象方式状态变化时会通知所有的观察者。
	
* Observer（观察者）

	负责接收来自Subject角色的状态变化的通知，由Observer接口扮演。
	
* ConcreteObserver（具体的观察者）

	实现Observer接口定义的update方法，获取被观察对象的最新状态并进行处理。

## 拓展思路
### 这里也出现了可替换性
	
	在Observer模式中，有带状态的ConcreteSubject角色和接收状态变化通知的ConcreteObserver角色，通过Subject角色和Observer角色完成连接。
	
	RandomNumberGenerator类并不知道，也无需在意正在观察自己的到底是谁，
	在observers字段中保存了所有的观察者，它们通过addObserver方法进行的注册，因此都实现了Observer接口。
	一定可以调用它们的update方法。
	
	观察者也不在意具体的被观察者是谁，它们都是NumberGenerator的子类，都有getNumber方法。
	
	可替换性设计帮助我们轻松的完成类的替换：
	* 利用抽象类和接口从具体类中抽出抽象方法
	* 在将实例作为参数传递至类中，或者在类的字段中保存实例时，不使用具体类型，而是使用抽象类型和接口。
	
	
### Observer的顺序

	保持具体观察者的独立性，避免因调用顺序引发故障。
	
### 当Observer的行为会对Subject产生影响时

	避免Obsever角色引发Subject发生状态变化，从而引发循环调用。
	
### 传递更新信息的方式

	NumberGenerator利用update方法告诉Observer自己的状态发生了更新，并没有具体的数值，Observer需要调用其getNumber来获取足够的数据。
	
	Subject角色向Observer角色传递信息的方式：
	* void update(NumberGenerator generator);
		
		只传递Subject角色，Observer角色可以从Subject角色中获取数据。
		
	* void update(NumberGenerator generator, int number);
	
		在传递Subject角色的同时，也传递了具体的数据，Observer角色可以直接获取到数据了，但同时Subject也需要了解到Observer角色关心的数据了。
		
	* void update(int number);
	
		最简单的实现方式，可以支持示例程序，但是当一个观察者观察多个对象时，没办法识别是哪个对象发生了变化。
	
	
### 从“观察”变为“通知”

	本质上是订阅通知机制。
	
### Model/View/Controller(MVC)

	大家听说过MVC吗？MVC中的Model和View的关系与Subject角色和Observer角色的关系相对应。
	Model是指操作“不依赖于显示形式的内部模型”的部分，View则是管理Model“怎样显示”的部分，通常一个Model对应多个View。

## 延伸阅读 java.util.Observer接口

	Java类库中的java.util.Observer接口和java.util.Observable类就是一种Observer模式。
	
	java.util.Observer接口总定义了以下方法：
	* public void update(Observable obj, Object arg)
	
	update方法的参数：
	* Observable类的实例时被观察的Subject角色
	* Object类的实例时附加信息
	
	但是java.util.Observer接口和java.util.Observable类并不好用，update方法决定了Subject角色必须是java.util.Observable类型，
	Java只能单一继承，如果Subject角色已经是某个类的子类，就不能继承java.util.Observable类。

##  相关设计模式
* Mediator模式

	在Mediator模式中，有时会使用Observer模式来实现Mediator角色与Colleague角色的通信。
	
	在Mediator模式中，发送通知是为了对Colleague角色进行仲裁。
	在Observer模式中，发送通知是为了Subject角色和Observer角色同步。