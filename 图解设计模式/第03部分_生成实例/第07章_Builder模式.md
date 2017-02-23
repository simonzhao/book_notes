# Builder模式

组装复杂实例

## 示例程序

```Java
    //Builder.java
    public abstract class Builder {
        public abstract void makeTitle(String title);
        public abstract void makeString(String str);
        public abstract void makeItems(String[] items);
        public abstract void close();
    }
    
    //Director.java
    public class Director {
        private Builder builder;
        public Director(Builder builder) {
            this.builder = builder;
        }
        
        public void construct() {
            builder.maketitle("Greeting");
            builder.makeString("从早上到下午");
            builder.makeItems( new String[]{
                "早上好。",
                "下午好。",
            });
            builder.makeString("晚上");
            builder.makeItems( new String[]{
                "晚上好。",
                "晚安。",
                "再见。",
            });
            builder.close();
        }
    }
    
    //TextBuilder.java
    public class TextBuilder extends Builder {
        private StringBuffer buffer = new StringBuffer();
        
        public void makeTitle(String title) {
            buffer.append("==================\n");
            buffer.append("[" + title + "]\n");
            buffer.append("\n");
        }
        
        public void makeString(String str) {
            buffer.append("* " + str + "\n");
            buffer.append("\n");
        }
        
        public void makeItems(String[] items) {
            for(int i=0; i < items.length; i++){
                buffer.append("    . " + itmes[i] + "\n");
            }
            buffer.append("\n");
        }
        
        public void close(){
            buffer.append("===================\n");
        }
        
        public String getResult() {
            return buffer.toString();
        }
    }
    
    //HTMLBuilder.java
    import java.io.*;
    public class HTMLBuilder extends Builder {
        private String filename;
        private PrintWriter writer;
        
        public void makeTitle(String title){
            filename = title + ".html";
            try {
                writer = new PrintWriter(new FileWriter(filename));
            } catch( IOException e) {
                e.printStackTrace();
            }
             writer.println("<html><head><title>" + title + "</title></head><body>");
             writer.println("<h1>" + title + "</h1>");
        }
        
        public void makeString(String str) {
            writer.println("<p>" + str + "</p>");
        }
        
        public void makeItems(String[] items) {
            writer.println("<ul>");
            for(int i=0; i < items.length; i++){
                writer.println("<li>" + itmes[i] + "</li>");
            }
            writer.println("</ul>");
        }
        
        public void close(){
            writer.println("</body></html>");
            writer.close();
        }
        
        public String getResult() {
            return filename;
        }
    }
    
    //Main.java
    public class Main {
        public static void main(Stirng[] args) {
            if (args.length != 1){
                usage();
                System.exit(0);
            }
            
            if(args[0].equals("plain")) {
                TextBuilder textbuilder = new TextBuilder();
                
                Director director = new Director( textbuilder );
                director.construct();
                String result = textbuilder.getResult();
                System.out.println( result );
            } else if (args[0].equals("html")) {
                HTMLBuilder htmlbuilder = new HTMLBuilder();
                Director director = new Director( htmlbuilder );
                director.construct();
                String filename = htmlbuilder.getResult();
                System.out.println(filename + "文件编写完成");
                
            } else {
                usage();
                System.exit(0);
            }
        }
        
        public static void usage() {
            System.out.println("Usage java Main plain 编写纯文本文档");
            System.out.println("Usage java Main html 编写html文档");
        }
    }
```
    
## 角色
* Builder(建造者)
    
    定义用于生成实例的接口，由Builder类扮演。

* ConcreteBuilder（具体的建造者）

    实现Builder角色定义的接口的类。还定义了获取最终生成的结果的方法。由TextBuilder类和HTMLBuilder类扮演。
    
* Director（监工）

    使用Builder角色的接口来生成实例，不依赖ConcreteBuilder角色，只调用在Builder角色中定义的方法。由Director类扮演。
    
* Client（使用者）

    使用Builder模式，也可以认为它不属于Builder模式。由Main类扮演。
    
## 思路
* 谁知道什么

    Main类没有调用Builder类，只是调用了Direct类的construct方法，Direct类就会开始工作（Main类对此一无所知），并完成文档的生成。
    
    Director类知道Builder类，它调用Builder类的方法来生成文档，但是并不知道真正使用的是哪个类，到底是TextBuilder还是HTMLBuilder，并不重要。
    
    因为Director类不知道具体使用的是哪个子类，**只有不知道子类才能替换**。 不论哪个子类都可以正常工作。
    
    因为不知道才能替换，因为可以替换，组件就具有高价值，**可替换性** 是在设计阶段重点考虑的。
    
    
* 设计时能够决定的事情和不能决定的事情

    在Builder类中，声明必需的所有方法。Director类中使用的方法都是Builder中提供的，因此，在Builder类中应当定义那些方法是非常重要的。
    
    Builder类必须能够应对未来子类可能增加的需求。
    
    
* 代码的阅读方法和修改方法

    只是阅读抽象类的代码无法获取很多信息。必须通观全局。
    
    如果修改Builder类，将会影响到Director类中调用Builder类的方法好Builder类的子类。
    
    如果修改了Director类，在其内部调用了TextBuilder类的特有方法，会导致其失去作为可复用组件的独立性，而且替换为其他子类时也将无法工作。
## 相关模式
* Template Method模式
    
    在Builder模式中，Director角色控制Builder角色。
    
    在Template Method模式中，父类控制子类。

* Composite模式

    在某些情况下Builder模式生成的实例构成了Composite模式。
    
* Abstract Factory模式

    Builder模式和Abstract Factory模式都用于生成复杂的实例。
    
* Facade模式

    在Builder模式中，Director角色通过组合Builder角色中的复杂方法向外部提供可以简单生成实例的接口。
    
    Facade模式中的Facade角色则是通过组合内部模块向外部提供可以简单调用的接口。