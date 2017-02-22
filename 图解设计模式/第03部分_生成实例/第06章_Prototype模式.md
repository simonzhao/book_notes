# Prototype模式 原型模式

通过复制生成实例

通常采用以下方式生成实例：
    
    new Something();
    
在不指定类名的前提下生成实例，而要根据现有的实例来生成新的实例：

* 对象种类繁多，无法将它们整合到一个类中。
* 难以根据类来生成实例时
* 解耦框架与生成的实例时


## 示例程序

    //framework/Product.java
    package framework;
    public interface Product extends Cloneable {
        public abstract void use(String s);
        public abstract Product createClone();
    }
    
    //framework/Manager.java
    package framework;
    import java.util.*;
    public class Manager {
        private HashMap showcase = new HashMap();
        public void register(String name, Product proto) {
            showcase.put(name, proto);
        }
        
        public Product create(String protoname) {
            Product p = (Product)showcase.get(protoname);
            return p.createClone();
        }
    }
    
    //MessageBox.java
    import framework.*;
    public class MessageBox implements Product {
        private char decochar;
        public MessageBox(char decochar) {
            this.decochar = decochar;
        }
        
        public void use(String s) {
            int length = s.getBytes().length;
            for(int i=0; i< length+4; i++ ){
                System.out.print(decochar);
            }
            System.out.println("");
            
            System.out.println(decochar + " " + s + " " + decochar);
            
            for(int i=0; i< length+4; i++ ){
                System.out.print(decochar);
            }
            System.out.println("");
        }
        
        public Product createClone() {
            Product p = null;
            try {
                p = (Product)clone();
            } catch ( CloneNotSupportedException e) {
                e.printStackTrace();
            }
        }
    }
    
    /* 输出
    *********
    * Hello *
    *********
    
    */
    
    //UnderlinePen.java
    import framework.*;
    public class UnderlinePen implements Product {
        private char ulchar;
        public UnderlinePen(char ulchar){
            this.ulchar = ulchar;
        }
        
        public void use(String s) {
            int length = s.getBytes().length;
            System.out.println("\"" + s + "\"");
            for(int i=0; i < length; i++){
                System.out.print(ulchar);
            }
            System.out.println("");
        }
        
        public Product createClone() {
            Product p = null;
            try {
                p = (Product)clone();
            } catch ( CloneNotSupportedException e) {
                e.printStackTrace();
            }
        }
    }
    
    /* 输出
    "Hello"
    ~~~~~~~
    
    */
    
    //Main.java
    import framework.*;
    
    public class Main{
        public static void main(String[] args){
            //准备
            Manager manager = new Manager();
            UnderlinePen upen = new UnderlinePen('~');
            MessageBox mbox = new MessageBox('*');
            MessageBox sbox = new MessageBox('/');
            
            manager.register("strong message", upen);
            manager.register("warning box", mbox);
            manager.register("slash box", sbox);
            
            //生成
            Product p1 = manager.create("strong message");
            p1.use("Hello, world.");
            Product p2 = manager.create("warning box");
            p2.use("Hello, world.");
            Product p3 = manager.create("slash box");
            p3.use("Hello, world.");
        }
    }
    
    /* 输出
    "Hello, world."    <- p1.use
    ~~~~~~~~~~~~~~~
    
    ****************
    * Hello, world *   <- p2.use
    ****************
    
    ////////////////
    / Hello, world /   <- p3.use
    ////////////////
    */


## 角色
* Prototype（原型）

    负责定义用于复制现有实例来生成新实例的方法。有Product接口扮演。

* ConcretePrototype（具体的原型）

    ConcretePrototype角色负责实现复制现有实例并生成新实例的方法。由MessageBox类和UnderlinePen类扮演。

* Client（使用者）

    负责使用复制实例的方法生成新的实例，有Manager类扮演。
    
## 思路
* 不能根据类来生成实例吗？
    * 对象种类繁多，示例程序中出现了3种样式，但是其实可以很多种，如果没种都生成类的话，类将非常多，管理起来十分混乱。
    
    * 难以通过类生成实例，用户通过鼠标操作画图，通过类来实现实例，怎样做？通过实例生成实例将比较容易。
    
    * 解耦框架与生成实例，在Manager类的create方法通过“strong message”来生成新的实例，相比传统方式更有通用性。
    
* 类名是束缚吗？
    面向对象编程的目标之一，就是“作为组件进行复用”。
    
    一旦在代码中出现要使用的类的名字，就无法与该类分离开来，也就无法实现复用。
    
    没有.java文件也能复用该类才是关键。

## 相关模式
* Flyweight模式
    使用Prototype模式可以生成一个与当前实例的
* Memento模式
* Composite模式
* Command模式

## 延伸
