# Facade模式 外观模式

程序总会变的越来越大，类越来越多，调用关系越来越复杂。
通过Facade模式，为系统提供统一的接口（API），在其内部整理调用关系与依赖。

## 示例程序
### Database类
	
	```java
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
	```
	
### HtmlWriter类
### PageMaker类
### Main类

## 角色
* Facade（窗口）
* 构成系统的许多其他角色
* Client（请求者）

## 拓展思路
### Facade角色到底做什么工作？
### 递归地使用Facade模式
### 开发人员不愿意创建Facade角色的原因——心理原因

## 相关设计模式
