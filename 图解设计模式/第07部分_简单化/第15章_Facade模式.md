# Facade模式 外观模式

程序总会变的越来越大，类越来越多，调用关系越来越复杂。
通过Facade模式，为系统提供统一的接口（API），在其内部整理调用关系与依赖。

## 示例程序
### Database类
	
```Java
// pagemaker/Database.java
package pagemaker;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;
	
public class Database {
	private Database() {
	}
	public static Properties getProperties(String dbname) {
		String filename = dbname + ".txt";
		Properties prop = new Properties();
		try {
			prop.load( new FileInputStream(filename) );
		} catch (IOException e) {
			Systerm.out.println("Warning: " + filename + " is not fountd." );
		}
		return prop;
	}
}

// maildata.txt
hyuki@hyuki.com=Hiroshi Yuki
hanako@hyuki.com=Hanako Sato
tomura@hyuki.com=Tomura
mamoru@hyuki.com=Mamoru Takahashi
```
	
### HtmlWriter类

```Java
// pagemaker/HtmlWriter.java
package pagemaker;

import java.io.Writer;
import java.io.IOException;

public class HtmlWriter {
	private Writer writer;
	
	public HtmlWriter(Writer writer){
		this.writer = writer;
	}
	public void title(String title) throws IOException {
		writer.write("<html>\n");
		writer.write("	<head>\n");
		writer.write("		<title>" + title + "</title>\n");
		writer.write("	</head>\n");
		writer.write("	<body>\n");
		writer.write("		<h1>" + title + "</h1>\n");
	}
	
	public void paragraph(String msg) throws IOException {
		writer.writer( "<p>" + msg + "</p>\n");
	}
	
	public void link(String href, String caption) throws IOException {
		write.writer( "<a href=\"" + href +"\">" + caption + "</a>");
	}
	
	public void mailto(String mailaddr, String username) throws IOException {
		write.writer("mailto:" + mailaddr, username);
	}
	
	public void close() throws IOException {
		writer.write("	</body>");
		writer.write("</html>");
		writer.close();
	}
}
```

### PageMaker类

```Java
// pagemaker/PageMaker.java
package pagemaker;

import java.io.FileWriter;
import java.io.IOException;
import java.util.Properties;

public class PageMaker {
	private PageMaker() {
	}
	public static void makeWelcomePage(String mailaddr, String fileanme){
		try {
			Properties mailprop = Database.getProperties("maildata");
			String username = mailprop.getProperty(mailaddr);
			
			HtmlWriter writer = new HtmlWriter( new FileWriter(filename) );
			writer.title("Weclome to " + username + "'s page!");
			writer.paragraph( username + " 欢迎来到 " + username + " 的主页。" );
			writer.paragraph("等着你的邮件哦！");
			writer.mailto(mailaddr, username);
			writer.close();
			System.out.println(filename + " is created for " + mailaddr + " (" + username + ")" );
		} catch (IOException e){
			e.printStackTrace();
		}
	}
}
```
### Main类

```Java
// Main.java
import pagemaker.PageMaker;

public class Main {
	public static void main(String[] args) {
		PageMaker.makeWelcomePage("hyuki@hyuki.com", "welcome.html");
	}
}
```
## 角色
* Facade（窗口）
	Facade角色是代表构成系统的许多其他角色的“简单窗口”，Facade角色向系统外部提供简单接口。
 
* 构成系统的许多其他角色
	这些角色各自工作，由Facade角色对他们进行组织，它们不需要了解Facade角色。
	
* Client（请求者）
	从系统外部调用Facade角色。

## 拓展思路
### Facade角色到底做什么工作？

完成对外部的屏蔽信息，减少依赖。

### 递归地使用Facade模式

递归的使用Facade模式，可以简化超大系统的维护压力。

### 开发人员不愿意创建Facade角色的原因——心理原因

**调用这个类之前需要先调用那个类。**
**调用这个方法之前需要先在那个类里注册一下。**

如果能够明确的用语言描述出来的知识，就用代码表现出来。

## 相关设计模式
* Abstract Factory模式
	生成复杂实例时的Facade模式。因为它提供了“要生成这个实例只需要调用这个方法就OK了”的简单接口。
	
* Singleton模式
	有时会采用Singleton模式创建Facade角色。
	
* Mediator模式
	Facade角色单方面的使用其他角色来提供高层接口。
	Mediator模式中的Mediator角色作为Colleague角色间的仲裁者负责调停。
	**Facade模式是单向的，Mediator角色是双向的。**
