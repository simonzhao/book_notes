# Memento模式 备忘录模式

使用Memento模式可以实现以下功能：
* Undo（撤销）
* Redo（重做）
* History（历史记录）
* Snapshot（快照）

## 示例程序

收集水果和获取金钱的掷骰子游戏。

* 游戏是自动进行的
* 游戏的主人公通过掷骰子来决定下一个状态
* 当骰子点数为1的时候，主人公的金钱会增加
* 当骰子点数为2的时候，主人公的金钱会减少
* 当骰子点数为6的时候，主人公得到水果
* 主人公没有钱时游戏就会结束

### Memento类

```Java
//game/Memento.java
/**
 * 没有声明可见类型是为了同包可见
 */
package game;
import java.util.*;
public class Memento {
	int money;
	ArrayList fruits;
	
	Memento(int money){
		this.money = money;
		this.fruits = new ArrayList();
	}
	
	void addFruit(String fruit){
		fruits.add(fruit);
	}
	List getFruits() {
		return (List)fruits.clone();
	}
}
```
### Gamer类
//game/Gamer.java
package game;
import java.util.*;

public class Gamer {
	private int money;
	private List fruits = new ArrayList();
	private Random random = new Random();
	private static String[] fruitsname = {
		"苹果", "葡萄"，"香蕉"，"橘子",
	}
	
	public Gamer(int money) {
		this.money = money;
	}
	
	public int getMoney(){
		return money;
	}
	
	public void bet(){
		int dice = random.nextInt(6) + 1;
		if ( dice == 1 ){
			money += 100;
			System.out.println("所持金钱增加了。");
		} else if ( dice == 2 ) {
			money / 2;
			System.out.println("所持金钱减半了。")
		} else if ( dice == 6 ) {
			String f = getFruit();
			System.out.println("获得了水果(" + f + ")");
			fruits.add(f);
		} else {
			System.out.println("什么都没有发生。")
		}
	}
	
	public Memento createMemento() {
		Memento m = new Memento(money);
		Iterator it = fruits.iterator();
		while(it.hasNext()){
			String f = (String)it.next();
			if ( f.startsWith("好吃的") ){
				m.addFruit(f);
			}
		}
		return m;
	}
	
	public void restoreMemento(Memento memento){
		this.money = memento.money;
		this.fruits = memento.getFruits();
	}
	
	public String toString() {
		return "[money=" + money + ", fruits = " + fruits + "]";
	}
	
	public String getFruit() {
		String prefix = "";
		if (random.nextBoolean()){
			prefix = "好吃的";
		}
		return prefix + fruitsname[ random.nextInt(fruitsname.length) ];
	}
}
### Main类
```java
//Main.java

import game.Memento;
import game.Gamer;

public class Main {
	public static void main(String[] args) {
		Gamer gamer = new Gamer(100);
		Memento memento = gamer.createMemento();
		for(int i = 0; i < 100; i++) {
			System.out.println("=====" + i);
			System.out.println("当前状态:" + gamer);
			
			gamer.bet();
			
			System.out.println("所持金钱为" + gamer.getMoney() + "元");
			
			if ( gamer.getMoney() > memento.getMoney() ){
				System.out.println("(所持金钱增加了许多，因此保存游戏当前的状态)");
				memento = gamer.createMemento();
			} else if ( game.getMoney() < memento.getMoney() / 2 ) {
				System.out.println("(所持金钱减少了许多，因此将游戏恢复至以前的状态)");
				game.restoreMemento(memento);
			}
			
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e ) {
			}
			System.out.println("");
		}
	}
}
```

## 角色
* Originator（生成者）

	Originator角色会在保存自己的最新状态时生成Memento角色。
	当把以前保存的Memento角色传递给Originator角色时，它会将自己恢复至生成该memento角色时的状态。
	由Gamer类扮演。
	
* Memento（纪念品）

	Memento角色会将Originator角色的内部信息整合在一起。在Memento角色中虽然保存了Originator角色的信息，但它不会向外部公开。
	
	Memento角色有以下两种接口（API）：
	* wide interface——宽接口
		
		Memento角色提供的“宽接口”是指所有用于获取恢复对象抓鬼女托信息的方法的集合。由于会暴露所有的内部信息，因此只有Originator角色可以使用。
		
	* narrow interface——窄接口
	
		Memento角色为外部Cartaker角色提供了“窄接口”。可以获取有限的信息，防止信息泄露。
		
* Caretaker（负责人）

	当Caretaker角色想要保存当前的Originator角色的状态时，会通知Originator角色。
	Originator就是在接收到通知后会生成memento角色的实例并将其返回给Caretaker角色。
	由于以后可能会用memento实例来将Originator恢复至原来状态，因此需要一直保存。
	
	Caretaker角色只能使用Memento角色的窄接口。
	**它只是将Originator角色生成的Memento角色当作一个黑盒子保存起来。**

## 拓展思路
### 两种接口（API）和可见性

	Java语言的可见性
		| 可见性 | 说明 |
		|-------|------|
		|public|所有类都可以访问|
		|protected|同一包中的类或是该类的子类可以访问|
		|无|同一包中的类可以访问|
		|private|只有该类自身可以访问|

	在Memento类中的可见性
		|可见性|字段，方法，构造函数|哪个类可以访问|
		|-------|------|------|
		| 无 | money | Memento类，Gamer类 |
		| 无 | fruits | Memento类，Gamer类 |
		| public | getMoney | Memento类，Gamer类， Main类 |
		| 无 | Memento | Memento类，Gamer类 |
		| 无 | addFruit | Memento类，Gamer类 |

	在Memento类中，只有getMoney方法是public的，它是一个“窄接口”，因此扮演Caretaker角色的Main类可以调用它。

### 需要多少个Memento

	如果在Main类中使用数组等集合，可以保存多个状态。
	
### Memento的有效期限是多久

	如果将Memento角色保存在磁盘文件中，需要注意新旧版本兼容问题。
	
### 划分Caretaker角色和Originator角色的意义

	Caretaker角色的职责是决定何时拍摄快照、何时撤销以及保存Memento角色。
	
	Originator角色的职责是生成Memento角色，使用接收到的Memento角色来恢复自己的状态。

## 相关的设计模式

* Command模式

	在使用Command模式处理命令时，可以使用Memento实现撤销功能。
	
* Protype模式

	在Memento模式中，为了能够实现快照和撤销功能，保存了对象当前的状态，保存的信息只是在恢复状态时所需要的那一部分。
	
	在Protype模式中，会生成一个与当前实例完全相同的另一个实例。
	
* State模式

	在Memento模式中，是用“实例”表示状态。
	
	在State模式中，是用“类”表示状态。