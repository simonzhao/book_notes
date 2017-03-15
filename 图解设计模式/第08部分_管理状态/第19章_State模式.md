# State模式  状态模式

用类来表示状态

## 示例程序
### 金库报警系统

* 有一个金库
* 金库与警报中心相连
* 金库里有警铃和正常通话用的电话
* 金库里有时钟，监视这现在的时间
* 白天的时间范围是9：00——16：59， 晚上的时间是：17：00——23：59和0：00——8：59
* 金库只能在白天使用
* 白天使用金库的话会在警报中心留下记录
* 晚上使用金库的话，会向警报中心发送紧急事态通知
* 任何时候都可以使用警铃
* 使用警铃的话，会向警报中心发送紧急事态通知
* 任何时候都可以使用电话，但是晚上只有留言电话
* 白天使用电话的话，会呼叫警报中心
* 晚上用电话的话，会呼叫警报中心的留言电话

### 不使用State模式的伪代码

```Java
警报系统的类 {
	使用金库时调用的方法() {
		if(白天) {
			向警报中心报告使用记录
		} else {
			向警报中心报告紧急事态
		}
	}
	警铃响起时调用的方法(){
		向警报中心报告紧急事态
	}
	正常通话时被调用的方法() {
		if(白天) {
			呼叫报警中心
		} else {
			呼叫报警中心的留言电话
		}
	}
}
```

### 使用State模式的伪代码

```Java
表示白天状态的类 {
	使用金库时调用的方法() {
		向警报中心报告使用记录
	}
	警铃响起时调用的方法(){
		向警报中心报告紧急事态
	}
	正常通话时被调用的方法() {
		呼叫报警中心
	}
}
表示晚上状态的类 {
	使用金库时调用的方法() {
		向警报中心报告紧急事态
	}
	警铃响起时调用的方法(){
		向警报中心报告紧急事态
	}
	正常通话时被调用的方法() {
		呼叫报警中心的留言电话
	}
}
```

### State接口

```Java
//State.java
public interface State {
	public abstract void doClock(Context context, int hour);
	public abstract void doUse(Context context);
	public abstract void doAlarm(Context context);
	public abstract void doPhone(Context context);
}
```

### DayState类

```Java
//DayState.java
public class DayState implements State {
	private static DayState singleton = new DayState();
	private DayState() {
	}
	public static state getInstance() {
		return singleton;
	}
	public void doClock(Context context, int hour) {
		if (hour < 9 || 17 <= hour) {
			context.changeState(NightState.getInstance());
		}
	}
	public void doUse(Context context){
		context.recordLog("使用金库（白天）");
	}
	public void doAlarm(Context context) {
		context.callSecurityCenter("按下警铃（白天）");
	}
	public void doPhone(Context context){
		context.callSecurityCenter("正常通话（白天）");
	}
	public String toString(){
		return "[白天]";
	}
}
```

### NightState类

```Java
//NightState.java
public class NightState implements State {
	private static NightState singleton = new NightState();
	private NightState() {
	}
	public static state getInstance() {
		return singleton;
	}
	public void doClock(Context context, int hour) {
		if (9 <= hour || 17 < hour) {
			context.changeState(DayState.getInstance());
		}
	}
	public void doUse(Context context){
		context.recordLog("紧急：晚上使用金库！");
	}
	public void doAlarm(Context context) {
		context.callSecurityCenter("按下警铃（晚上）");
	}
	public void doPhone(Context context){
		context.callSecurityCenter("晚上的通话录音");
	}
	public String toString(){
		return "[晚上]";
	}
}
```
### Context接口

```Java
//Context.java
public interface Context {
	public abstract void setClock(int hour);
	public abstract void changeState(State state);
	public abstract void callSecurityCenter(String msg);
	public abstract void recordLog(String msg);
}
```

### SafeFrame类

```Java
import java.awt.Frame;
import java.awt.Label;
import java.awt.Color;
import java.awt.Button;
import java.awt.TextField;
import java.awt.TextArea;
import java.awt.Panel;
import java.awt.BorderLayout;
import java.awt.event.ActionListener;
import java.awt.event.ActionEvent;

public class SafeFrame extends Frame implements ActionListener, Context {
	private TextField textClock = new TextField(60); //显示当前时间
	private TextField textScreen = new TextField(10, 60); //显示警报中心的记录
	private Button buttonUse = new Button("使用金库");
	private Button buttonAlarm = new Button("按下警铃");
	private Button buttonPhone = new Button("正常通话");
	private Button buttonExit = new Button("结束");
	
	private State state = DayState.getInstance();
	
	public SafeFrame(String title) {
		super(title);
		setBackground(Color.lightGray);
		setLayout(new BorderLayout());
		
		add(textClock, BorderLayout.NORTH);
		textClock.setEditable(false);
		add(textScreen, BorderLayout.CENTER);
		textScreen.setEditable(false);
		
		Panel panel = new Panel();
		panel.add(buttonUse);
		panel.add(buttonAlarm);
		panel.add(buttonPhone);
		panel.add(buttonExit);
		
		add(panel, BorderLayout.SOUTH);
		
		pack();
		show();
		
		buttonUse.addActionListener(this);
		buttonAlarm.addActionListener(this);
		buttonPhone.addActionListener(this);
		buttonExit.addActionListener(this);
	}
	
	public void actionPerformed(ActionEvent e) {
		System.out.println(e.toString());
		if(e.getSource() == buttonUse){
			state.douse(this);
		} else if (e.getSource() == buttonAlarm){
			state.doAlarm(this);
		} else if (e.getSource() == buttonPhone){
			state.doPhone();
		} else if (e.getSource() == buttonExit){
			System.exit(0);
		} else {
			System.out.println("?");
		} 
	}
	
	public void setClock(int hour) {
		String clockstring = "现在时间是";
		if(hour < 10) {
			clockstring += "0" + hour + ":00";
		} else {
			clockstring += hour + ":00";
		}
		System.out.pringln(clockstring);
		textClock.setText(clockstring);
		state.doClock(this, hour);
	}
	
	public void changeState(State state) {
		System.out.println("从" + this.state + "状态变为了" + state + "状态。");
		this.state = state;
	}
	
	public void callSecurityCenter(String msg) {
		textScreen.append("call! " + msg + "\n");
	}
	
	public void recordLog(String msg) {
		textScreen.append("record..." + msg + "\n");
	}
}
```

### Main类

```Java
//Main.java

public class Main {
	public static void main(String[] args){
		SafeFrame frame = new SafeFrame("State Sample");
		while(true) {
			for(int hour = 0; hour < 24; hour++) {
				frame.setClock(hour);
				try {
					Thread.sleep(1000);
				} catch (InterruptedException e) {
				}
			}
		}
	}
}
```

## 角色
* State（状态）

	State角色表示状态，定义了根据不同状态进行不同处理的接口。该接口是那些处理内容依赖于状态的方法的集合。
	
* ConcreteState（具体的状态）
	
	ConcreteState角色表示各个具体的状态，它实现State接口。
	
* Context（状况、前后关系、上下文）

	Context角色持有当前状态的ConcreteState角色。

## 扩展思路
### 分而治之

	将一个复杂的大问题，分解为多个小问题
	
### 依赖于状态的处理

	* 定义接口，声明抽象方法
	* 定义多个类，实现具体方法
	
### 应当是谁来管理状态迁移

	示例程序中是有ConcreteState角色来实际管理状态的变化，但是也可以由Context角色来完成。
	
### 不会自相矛盾

	如果不使用State模式，就需要多个变量的值来表示系统的状态，要注意不要矛盾。
	
### 易于增加新的状态

	State模式增加新的状态就是新增一个类。
	
### 实例的多面性

	SafeFrame类中有以下两个语句：
	* SafeFrame 类的构造函数
		buttonUse.addActionListener(this);
	* actionPerformed方法中
		state.doUse(this);
		
	向addActionListener方法传递this是，该实例会被当作“实现了ActionListener接口的类的实例”来处理。
	
	向doUse方法传递this时，该实例会被当作”实现了Context接口的类的实例“来处理。

## 相关设计模式
* Singleton模式

	Singleton模式常常会出现在ConcreteState角色中。
	
* Flyweight模式

	在表示状态的类中并没有定义任何实例字段，有时我们可以使用Flyweight模式在多个Context角色之间共享ConcreteState角色。