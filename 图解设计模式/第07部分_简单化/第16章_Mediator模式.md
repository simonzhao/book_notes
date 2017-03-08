# Mediator模式 仲裁者模式

只有一个仲裁者，组员向仲裁者报告，仲裁者向组员下达指示。

## 实例程序
### Mediator接口

```Java
//Mediator.java
public interface Mediaor {
	public abstract void createColleagues();
	public abstract void colleagueChanged();
}
```

### Colleague接口

```Java
//Colleague.java
public interface Colleague {
	public abstract void setMediator(Mediator mediator);
	public abstract void setColleagueEnabled(boolean enabled);
}
```

### ColleagueButton类

```Java
//Colleague.java
import java.awt.Button;
public class ColleagueButton extends Button implements Colleague {
	private Mediator mediator;
	public ColleagueButton(String caption) {
		super(caption);
	}
	public void setMediator(Mediator mediator){
		this.mediator = mediator;
	}
	public void setColleagueEnable(boolean enabled){
		setEnabled(enabled);
	}
}
```
### ColleagueTextField类

```Java
//ColleagueTextField.java
import java.awt.TextField;
import java.awt.Color;
import java.awt.event.TextListener;
import java.awt.event.TextEvent;
public class ColleagueTextField extends TextField implements TextListener, Colleague {
	private Mediator mediator;
	public ColleagueTextFiedl(String text, int columns){
		super(text, columns);
	}
	public void setMediator(Mediator mediator){
		this.mediator = mediator;
	}
	public void setColleagueEnabled(boolean enabled){
		setEnabled(enabled);
		setBackground(enabled ? Color.white : Color.lightGray );
	}
	public void textValueChanged(TextEvent e){
		mediator.colleagueChanged();
	}
	
}
```
### ColleagueCheckbox类

```Java
//ColleaueCheckbox.java
import java.awt.Checkbox;
import java.awt.CheckboxGroup;
import java.awt.event.ItemListener;
import java.awt.event.ItemEvent;
public class ColleaueCheckbox extends Checkbox impolements ItemListener, Colleague {
	private Mediator mediator;
	public ColleagueCheckbox(String caption, checkboxGroup group, boolean state){
		super(caption, group, state);
	}
	public void setMediator(Mediator mediator){
		this.mediator = mediator;
	}
	public void setColleagueEnabled(boolean enabled){
		setEnabled(enabled);
	}
	/**
	 * 当状态发生变化时通知Mediator
	 */
	public void itemStateChanged(ItemEvent e){
		mediator.colleagueChanged();
	}
}
```
### LoginFrame类

```Java
//LoginFrame.java
import java.awt.Frame;
import java.awt.Label;
import java.awt.Color;
import java.awt.CheckboxGroup;
import java.awt.GridLayout;
import java.awt.event.ActionListener;
import java.awt.event.ActionEvent;

public class LoginFrame extends Frame implements ActionListener, Mediator {
	private ColleagueCheckbox checkGuest;
	private ColleagueCheckbox checkLoging;
	private ColleagueTextField textUser;
	private ColleagueTextField textPass;
	private ColleagueButton buttonOk;
	private ColleagueButton buttonCancel;
	
	public LoginFrame(String title) {
		super(title);
		
		setBackground(Color.lightGray);
		setLayout( new GridLayout(4, 2) );
		
		createColleagues();//生成各个Colleague
		
		add(checkGuest);
		add(checkLogin);
		add(new Label("Username:"));
		add(textUser);
		add(new Label("Password:"));
		add(textPass);
		add(buttonOk);
		add(buttonCancel);
		
		colleagueChanged();//设置初始状态
		
		pack();
		show();
	}
	
	public void createColleagues() {
		checkboxGroup g = new CheckboxGroup();
		checkGuest = new ColleagueCheckbox("Guest", g, true);
		checkLogin = new ColleagueCheckbox("Login", g, false);
		textUser = new ColleagueTextField("", 10);
		textPass = new ColleagueTextField("", 10);
		textPass.setEchoChar('*');
		buttonOk = new ColleagueButton("Ok");
		buttonCancel = new ColleagueButton("Cancel");
		
		checkGuest.setMediator(this);
		checkLogin.setMediator(this);
		textUser.setMediator(this);
		textPass.setMediator(this);
		buttonOk.setMediator(this);
		buttonCancel.setMediator(this);
		
		checkGuest.addItemListener(checkGuest);
		checkLogin.addItemListener(checkLogin);
		textUser.addTextListener(textUser);
		textPass.addTextListener(textPass);
		buttonOk.addActionListener(this);
		buttonCancel.addActionListener(this);
	}
	
	/**
	 * 接收来自Colleage的通知然后判断各个Colleage的启用|禁用状态
	 */
	public void colleagueChanged() {
		if (checkGuest.getState()) {
			//Guest Mode
			textUser.setColleagueEnabled(false);
			textPass.setColleagueEnabled(false);
			buttonOk.setColleagueEnabled(true);
		} else {
			//Login mode
			textuser.setColleagueEnabled(true);
			userpassChanged();
		}
	} 
	
	/**
	 * 当testUser 或 textPass 输入框中的文字发生变化时，各Colleage的启用|禁用状态
	 */
	private void userpassChanged() {
		if (textuser.getText().length() > 0 ) {
			textPass.setColleagueEnabled(true);
			if(textPass.getText().length() > 0 ){
				buttonOk.setColleagueEnabled(true);
			} else {
				buttonOk.setColleagueEnabled(false);
			}
		} else {
			buttonOk.setColleagueEnabled(false);
			textPass.setColleagueEnabled(false);
		}
	}
	
	public void actionPerformed(ActionEvent e) {
		System.out.println(e.toString());
		System.exit(0);
	}
}
```
### Main类

```Java
//Main.java
public class Main {
	public static void main(String[] args){
		new LoginFrame("Mediator Sample");
	}
}
```

## 角色
* Mediator（仲裁者、中介者）

	负责定义与Colleague角色进行通信和做出决定的接口，由Mediator接口扮演。
	
* ConcreteMediator（具体的仲裁者）

	实现Mediator角色定义的接口，实际做出决定，由LoginFrame类扮演。
	
* Colleague（同事）

	负责定义与Mediator进行通信的接口，由Colleague接口扮演。
	
* ConcreteColleague（具体的同事）

	负责实现Colleague角色的接口。ColleagueButton类等扮演。

## 拓展思路
### 当发生分散灾难时

	面向对象编程可以帮助我们进行分散处理，避免过于集中，但是如果只是将应当分散的分散，而没有将应当集中的集中起来的话，也将导致系统难以维护。
	
### 通信线路的增加

	假设A和B两个类需要互相通信，那么就是A->B和B-A，如果是A，B，C 3个类就是6个，随着相互通信的类的增加，通信的数量会快速增加，最终将导致灾难。
	
	Mediator模式将简化这些通信。即使项目开始时相互通信的类较少，从长远看，相互通信的类必然会增大。
	
### 哪些角色可以复用

	ConcreteColleague角色可以复用，ConcreteMediator角色很难复用。
	
	**依赖于特定应用程序就意味着难以复用**

## 相关设计模式
* Facade模式

	在Mediator模式中，Mediator角色与Colleague角色进行交互。
	在Facade模式中，Facade角色单方面的使用其他角色。
	
* Observer模式

	有时会使用Observer模式来实现Mediator角色与Colleague角色之间的通信。
