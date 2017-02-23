# Abstract Factory模式 抽象工厂模式

将关联零件组装成产品

**抽象工厂的工作是将“抽象零件”组装为“抽象产品”**

抽象在面向对象编程中的含义是“不考虑怎样实现，而是仅关注接口（API）的状态。抽象方法（Abstract Method）并不定义方法的具体实现，只确定方法的名字和签名（参数的类型和个数）。

**我们并不关心零件的具体实现，而是只关心接口（API）。我们仅使用该接口（API）将零件组装成为产品。**

在Template Method模式和Builder模式中，子类这一层负责方法的具体实现。在Abstract Factory模式中也是一样。在子类这一层中有具体的工厂，它负责将具体的零件组装成为具体的产品。

## 示例程序

将带有层次关系的链接的集合制作成html文件。

```html
    <html>
        <head>
            <title>LinkPage</title>
        </head>
        <body>
            <h1>LinkPage</h1>
            <ul>
                <li>
                    日报
                    <ul>
                        <li><a href="http://www.people.com.cn/">人民日报</a></li>
                        <li><a href="http://www.gmw.cn">光明日报</a></li>
                    </ul>
                </li>
                <li>
                    搜索引擎
                    <ul>
                        <li>
                            Yahoo!
                            <ul>
                                <li><a href="http://www.yahoo.com">Yahoo!</a></li>
                                <li><a href="http://www.yahoo.co.jp">Yahoo! Japan</a></li>
                            </ul>
                        </li>
                        <li><a href="http://www.excite.com">Excite</a></li>
                        <li><a href="http://www.google.com">Google</a></li>
                    </ul>
                </li>
            </ul>
            <hr>
            <address>杨文轩</address>
       </body>
    </html>

    <html>
        <head>
            <title>LinkPage</title>
        </head>
        <body>
            <h1>LinkPage</h1>
            <table width="80%" border="3">
                <tr>
                    <td>
                        <table width="100%" border="1">
                            <tr>
                                <td bgcolor="#cccccc" align="center" colspan="2">
                                    <b>日报</b>
                                </td>
                            </tr>
                            <tr>
                                <td><a href="http://www.people.com.cn/">人民日报</a></td>
                                <td><a href="http://www.gmw.cn/">光明日报</a></td>
                            </tr>
                        </table>
                    </td>
                </tr>
                <tr>
                    <td>
                        <table width="100%" border="1">
                            <tr>
                                <td bgcolor="#cccccc" align="center" colspan="3">
                                    <b>搜索引擎</b>
                                </td>
                            </tr>
                            <tr>
                                <td>
                                    <table width="100%" border="1">
                                        <tr>
                                            <td bgcolor="#cccccc" align="center" colspan="2">
                                                <b>Yahoo!</b>
                                            </td>
                                        </tr>
                                        <tr>
                                            <td><a href="http://www.yahoo.com/">Yahoo!</a></td>
                                            <td><a href="http://www.yahoo.co.jp/">Yahoo!Japan</a></td>
                                        </tr>
                                    </table>
                                </td>
                                <td><a href="http://www.excite.com/">Excite</a></td>
                                <td><a href="http://www.google.com/">Google</a></td>
                            </tr>
                        </table>
                    </td>
                </tr>
           </table>
           <hr>
           <address>杨文轩</address>
       </body>
    </html>
```

    包：
        1. factory包： 包含抽象工厂，零件，产品
        2. 无：包含Main类
        3. listfactory包：包含具体工厂、零件、产品的包

 ```Java
    /**
     * 抽象的零件 Item
     * 是Link类和Tray类的父类，
     * 这样Link类和Tray类就具有可替换性
     */
    //factory/Item.java
    package factory;
    public abstract class Item {
        protected String caption; //项目标题
        public Item(String caption){
            this.caption = caption;
        }
        public abstract String makeHtml();
    }
    
    //factory/Link.java
    /**
     * 抽象的零件 Link类
     * 抽象的表示Html中的超链接
     */
    package factory;
    public abstract class Link extends Item {
        protected String url;
        public Link(String caption, String url){
            super(caption);
            this.url = url;
        }
    }
    
    //factory/Tray.java
    /**
     * 抽象的零件 Tray类
     * 表示一个含有多个Link和Tray类的容器
     */
    package factory;
    import java.util.ArrayList;
    public abstract class Tray extends Item {
        protected ArrayList tray = new ArrayList();
        public Tray(String caption) {
            super(caption);
        }
        public void add(Item item){
            tray.add(item);
        }
    }
    
    //factory/Page.java
    package factory;
    import java.io.*;
    import java.util.ArrayList;
    /**
     *  抽象产品 Page类
     * 抽象的表示Html页面
     * 如果Link类和Tray类是抽象类的话
     * Page类就是抽象产品
     */
    public abstract class Page {
        protected String title;
        protected String author;
        protected ArrayList content = new ArrayList();
        public Page(String title, String author){
            this.title = title;
            this.author = author;
        }
        public void add(Item item){
            contend.add(item);
        }
        public void output(){
            try {
                String filename = title + ".html";
                Writer writer = new FileWriter(filename);
                writer.write( this.makeHTML() );
                writer.close();
                System.out.println(filename + " 编写完成。");
            } catch (IOException e ) {
                e.printStackTrace();
            }
        }
        public abstract String makeHTML();
    }
    
    //factory/Factory.java
    package factory;
    public abstract class Factory {
        public static Factory getFactory(String classname) {
            Factory factory = null;
            try {
                factory = (Factory)class.forName(classname).newInstance();
            } catch (ClassNotFoundException e) {
                System.err.println("没有找到" + classname + "类。");
            } catch ( Exception e ) {
                e.printStackTrace();
            }
            return factory;
        }
        public abstract Link createLink(String caption, String url);
        public abstract Tray createTray(String caption);
        public abstract Page createPage(String title, String author);
    }
    //Main.java
    import factory.*;
    /**
     * 使用工厂将零件组装成产品
     * 该类没有使用任何具体零件、产品和工厂
     */
    public class Main {
        public static void main(String[] args){
            if (args.length != 1) {
                System.out.println("Usage: java Main class.name.of.ConcreateFactory");
                System.out.println("Example 1: java Main listfactory.ListFactory");
                System.out.println("Example 2: java Main tablefactory.TableFactory");
            }
             
            Factory factory = Factory.getFactory(args[0]);
            
            Link people = factory.createLink("人民日报", "http://www.people.com.cn/");
            Link gmw = factory.createLink("光明日报", "http://www.gmw.cn/");
            
            Link us_yahoo = factory.createLink("Yahoo!", "http://www.yahoo.com/");
            Link jp_yahoo = factory.createLink("Yahoo!Japan", "http://www.yahoo.co.jp/");
            Link excite = factory.createLink("Excite!", "http://www.excite.com/");
            Link google = factory.createLink("Google", "http://www.google.com/");
            
            Tray traynews = factory.createTray("日报");
            traynews.add(people);
            traynews.add(gmw);
            
            Tray trayyahoo = factory.createTray("Yahoo!");
            trayyahoo.add(us_yahoo);
            trayyahoo.add(jp_yahoo);
            
            Tray traysearch = factory.createTray("搜索引擎");
            traysearch.add(trayyahoo);
            traysearch.add(excite);
            traysearch.add(google);
            
            Page page = factory.createPage("LinkPage", "杨文轩");
            page.add(traynews);
            page.add(traysearch);
            page.output();
        }
    }

    //listfactory/ListFactory.java
    /**
     * 具体的工厂 ListFactory类
     */
    package listfactory;
    import factory.*;
    public class ListFactory extends Factory {
        public Link createLink(String caption, String url) {
            return new ListLink(caption, url);
        }
        public Tray createTray(String caption) {
            return new ListTray(caption);
        }
        public Page createPage(String title, String author){
            return new ListPage(title, author);
        }
    }

    //listfactory/ListLink.java
    /**
     * 具体的零件 ListLink类
     */
    package listfactory;
    import factory.*;
    public class ListLink extends Link {
        public ListLink(String caption, String url) {
            super(caption, url);
        }
        public String makeHTML() {
            return " <li><a href=\"" + url +"\">" + caption + "</a></li>\n";
        }
    }

    //listfactory/ListTray.java
    /**
     * 具体的零件 ListTray类
     */
    package listfactory;
    import factory.*;
    import java.util.Iterator;
    public class ListTray extends Tray {
        public ListTray(String caption){
            super(caption);
        }
        public String makeHTML() {
            StringBuffer buffer = new StringBuffer();
            buffer.append("<li>\n");
            buffer.append(caption + "\n");
            buffer.append("<ul>\n");
            Iterator it = tray.iterator();
            while(it.hasNext()) {
                Item item = (Item)it.next();
                buffer.append(item.makeHTML());
            }
            buffer.append("</ul>\n");
            buffer.append("</li>\n");
            return buffer.toString();
        }
    }

    //listfactory/ListPage.java
    /**
     * 具体的产品
     */
    package listfactory;
    import factory.*;
    import java.util.Iterator;
    public class ListPage extends Page {
        public ListPage(Stirng title, String author) {
            super(title, author);
        }
        public Stirng makeHTML(){
            StringBuffer buffer = new StringBufer();
            buffer.append("<html><head><title>" + title + "</title></head>\n");
            buffer.append("<body>\n");
            buffer.append("<h1>" + title + "</h1>");
            buffer.append("<ul>\n");
            Iterator it = content.iterator();
            while( it.hasNext() ) {
                Item item = (Item)it.next();
                buffer.append( item.makeHTML() );
            }
            buffer.append("</ul>\n");
            buffer.append("<hr><address>" + author + "</address>");
            buffer.append("</body></html>");
            return buffer.toString();
        }
    }
    
    //tablefactory/TableFactory.java
    package tablefactory;
    import factory.*;
    public class TableFactory extends Factory {
        public Link createLink(String caption, String url) {
            return new TableLink(caption, url);
        }
        public Tray createTray(String caption) {
            return new TableTray(caption);
        }
        public Page createPage(String title, String author){
            return new TablePage(title, author);
        }
    }
    
    //tablefactory/TableLink.java
    /**
     * 具体的零件 TableLink
     */
    package tablefactory;
    import factory.*;
    public class TableLink extends Link {
        public TableLink(String caption, String url) {
            super(caption, url);
        }
        public String makeHTML() {
            return " <td><a href=\"" + url +"\">" + caption + "</a></td>\n";
        }
    }

    //tablefactory/TableTray.java
    /**
     * 具体的零件 ListTray类
     */
    package tablefactory;
    import factory.*;
    import java.util.Iterator;
    public class TableTray extends Tray {
        public TableTray(String caption){
            super(caption);
        }
        public String makeHTML() {
            StringBuffer buffer = new StringBuffer();
            buffer.append("<td>");
            buffer.append("<table width=\"100%\" border=\"1\"><tr>")
            buffer.append("<td bgcolor=\"#cccccc\" align=\"center\" colspan=\"" + tray.size() + "\"><b>" + caption + "</b></td>");
            buffer.append("</tr>\n");
            buffer.append("<tr>\n");
            Iterator it = tray.iterator();
            while(it.hasNext()) {
                Item item = (Item)it.next();
                buffer.append(item.makeHTML());
            }
            buffer.append("</tr></table>");
            buffer.append("</td>");
            return buffer.toString();
        }
    }

    //tablefactory/TablePage.java
    /**
     * 具体的产品
     */
    package tablefactory;
    import factory.*;
    import java.util.Iterator;
    public class TablePage extends Page {
        public TablePage(Stirng title, String author) {
            super(title, author);
        }
        public Stirng makeHTML(){
            StringBuffer buffer = new StringBufer();
            buffer.append("<html><head><title>" + title + "</title></head>\n");
            buffer.append("<body>\n");
            buffer.append("<h1>" + title + "</h1>");
            buffer.append("<table width=\"80%\" border=\"3\">\n");
            Iterator it = content.iterator();
            while( it.hasNext() ) {
                Item item = (Item)it.next();
                buffer.append("<tr>" +  item.makeHTML() + "</tr>");
            }
            buffer.append("</table>\n");
            buffer.append("<hr><address>" + author + "</address>");
            buffer.append("</body></html>");
            return buffer.toString();
        }
    }
```

## 角色
* AbstractProduct（抽象产品）

    定义AbstractFactory角色所生成的抽象零件和产品的接口（API）。在示例程序中有Link类、Tray类和Page类扮演。

* AbstractFactory（抽象工厂）

    负责定义用于生成抽象产品的接口（API）。在示例程序中，由Factory类扮演。

* Client（委托者)

    Client角色仅调用AbstractFactory角色和AbstractProduct角色的接口（API}来进行工作，对于具体的零件、产品和工厂一无所知。由Main类扮演。

* ConcreteProduct（具体产品）

    负责实现AbstractProduct角色的接口（API）。

* ConcreteFactory（具体工厂）

    负责实现AbstractFactory角色的接口（API）。

## 思路
* 易于增加具体的工厂

    增加新的具体工厂，编写子类实现抽象方法，不论增加多少都无需修改抽象工厂和Main部分。

* 难以增加新的零件

    如果增加新的零件，必须要修改所有具体工厂。

## 相关设计模式
* Builder模式

    Abstract Factory模式通过调用抽象产品的接口来组装抽象产品，生成具有复杂结构的实例。

    Builder模式则是分阶段地制作复杂实例。

* Factory Method模式

    有时Abstract Factory模式中零件和产品的生成会使用到Factory Method模式。

* Composite模式

    有时Abstract Factory模式在制作产品时会使用Composite模式。

* Singleton模式

    有时Abstract Factory模式中的具体工厂会使用Singleton模式。

## 延伸

在Java中可以使用下面的方法生成实例：
* new

    一般是这个 Something obj = new Something(); 此时类名会出现在代码中。

* clone

    使用Prototype模式中了解到的clone方法，根据实例复制出一个新的实例，**但是构造函数不会被调用**。

* newInstance

    通过调用Class类的newInstance方法可以生成实例，同时会调用无参构造函数。

    someobj.getClass().newInstance();

    调用newInstance方法会抛出异常InstantiationException,IllegalAccessException。要进行拦截处理。
