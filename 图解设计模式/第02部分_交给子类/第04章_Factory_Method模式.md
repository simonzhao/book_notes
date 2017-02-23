# Factory Method模式——工厂方法模式

将实例的生成交给子类

在Template Method模式中，父类规定处理流程，子类实现具体处理。如果将该模式用于生成实例，就是**Factory Method模式**。

## 示例程序

```Java
    // Product.java
    package framework;
    public abstract class Product {
        public abstract void use();
    }
    
    // Factory.java
    package framework;
    public abstract class Factory {
        public final Product create(String owner) {
            Product p = createProduct(owner);
            registerProduct(p);
            return p;
        }
        
        protected abstract Product createProduct(String owner);
        protected abstract void registerProduct(Product product);
    }
    
    //IDCard.java
    package idcard;
    import framework.*;
    
    public class IDCard extends Product {
        private String owner;
        IDCard(String owner) {
            System.out.println("制作" + owner + "的ID卡。");    
        }
        
        public void use() {
            System.out.println(""制作" + owner + "的ID卡。"");
        }
        
        public Stirng getOwner() {
            return owner;
        }
    }
    
    //IDCardFactory.java
    package idcard;
    import framework.*;
    import java.util.*;
    
    public class IDCardFactory extends Factory {
        private List owners = new ArrayList();
        protected Product createProduct(String owner) {
            return new IDCard(owner);
        }
        protected void registerProduct(Product product) {
            owners.add(((IDCard)product).getOwner());
        }
        public List getOwners(){
            return owners;
        }
    }
    
    //Main.java
    import framework.*;
    import idcard.*;
    public class Main {
        public static void main(String[] args) {
            Factory factory = new IDCardFactory();
            
            Product card1 = factory.create("小明");
            Product card2 = factory.create("小红");
            Product card3 = factory.create("小刚");
        
            card1.use();
            card2.use();
            card3.use();
        }
    }
 ```
   
    //运行结果
    制作小明的ID卡。
    制作小红的ID卡。
    制作小刚的ID卡。
    使用小明的ID卡。
    使用小红的ID卡。
    使用小刚的ID卡。
    
## 角色
* Product产品

    Product角色属于框架这一方，抽象类。定义Factory Method模式中生成的那些实例所持有的接口（API），具体的处理则由子类ConcreteProduct角色决定。
    
* Creator创建者

    Creator角色属于框架这一方，负责生成Product角色的抽象类，具体的处理有ConcreteCreator角色决定。
    
    **不用new关键字来生成实例，而是调用生成实例的专用方法来生成实例，这样就可以防止父类与其他具体类耦合**
    
* ConcreteProduct具体的产品

    ConcreteProduct角色属于具体加工这一方，决定了具体的产品。
    
* ConcreteCreator具体的创建者

    ConcreteCreator角色属于具体加工这一方，负责生成具体的产品。
    
## 思路
* 框架与具体加工
    
    采用“框架”与“具体加工”方式，在不修改框架的情况下，可以创建出其他产品。
    
    在framework包中，没有引入idcard包。在Product类和Factory类中，也没有出现IdCard和IdCardFactory等具体类的名字。
    
* 生成实例——方法的三种实现方式
    
    在示例程序中，Factory类的createProduct方法是抽象方法，需要在子类中实现该方法。有以下3中：
    * 指定其为抽象方法
    
        指定其为抽象方法，要求子类必须实现，否则编译阶段将报错。
        
    * 为其实现默认处理
        
        为其实现默认处理，如果子类没有实现即进行默认处理
    
    * 在其中抛出异常

        在其中抛出异常，如果子类没有实现将产生异常
        
* 使用模式与开发人员之间的沟通

## 相关的设计模式
* Template Method模式
    
    Factory Method模式是Template Method模式的典型应用。

* Singleton模式
    
    在多数情况下Signgletion模式用于扮演Creator角色（或是ConcreteCreator角色）的类。

* Composite模式
    
    有时可以将Composite模式用于Product角色。

* Iterator模式

    有时，在Iterator模式中使用iterator方法生成Iterator实例时会使用Factory Method模式。
