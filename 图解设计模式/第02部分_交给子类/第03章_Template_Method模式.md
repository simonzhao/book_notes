# Template Method模式 - 模版方法模式

**在父类中定义处理流程的框架，在子类中实现具体处理的模式**

## 示例程序

```Java
    public abstract class AbstractDisplay {
        public abstract void open();
        public abstract void print();
        public abstract void close();
        
        public final void display() {
            open();
            for(int i=0; i<5; i++){
                print();
            }
            close();
        }
    }
    
    public class CharDisplay extends AbstractDisplay {
        private char ch;
        public CharDisplay(char ch) {
            this.ch = ch;
        }
        
        public void open() {
            System.out.print("<<");
        }
        
        public void print() {
            System.out.print(ch);
        }
        
        public void close() {
            System.out.println(">>");
        }
    }
    
    public class StringDisplay extends AbstractDisplay {
        private String string;
        private int width;
        
        public StringDisplay(String string) {
            this.string = string;
            this.width = string.getBytes().length;
        }
        
        public void open() {
            printLine();
        }
        
        public void print() {
            System.out.println("|" + string + "|");
        }
        
        public void close() {
            printLine();
        }
        
        private void printLine() {
            System.out.print("+");
            for(int i=0; i < width; i++) {
                System.out.print("-");   
            }
            System.out.println("+");
        }
    }
    
    public class Main {
        public static void main(String[] args) {
            AbstractDisplay d1 = new CharDisplay('H');
            AbstractDisplay d2 = new StringDisplay("Hello, world.");
            
            d1.display();
            d2.display();
        }
    }
```  
 
    <<HHHHH>>           -> d1的显示结果
    +-------------+     -> d2的显示结果
    |Hello, world.|
    |Hello, world.|
    |Hello, world.|
    |Hello, world.|
    |Hello, world.|
    +-------------+
    
## 角色
* AbstractClass（抽象类）

    AbstractClass负责实现模版方法，声明模版方法要使用的抽象方法。
* ConcreteClass（具体类）

    负责实现AbstractClass中定义的抽象方法，这些方法会在AbstractClass中的模版方法中被调用。
    
## 扩展思路
* 可以使逻辑处理通用化

    在父类中编写算法，无需每个子类中再次编写。
* 父类和子类之间的协作

    父类与子类紧密联系、共同工作。必须了解子类实现的抽象方法的调用时机，在看不到父类源代码的情况下，编写子类是比较困难的。
* 父类与子类的一致性

    **里氏替换原则（The liskov Substitution Principle, LSP）**:无论在父类类型的变量保存哪个子类的实例，程序都可以正常工作。
    
## 相关设计模式
* Factory Method模式

    Factory Method 模式是将 Template Method模式用于生成实例的一个典型例子。
    
* Strategy模式

    在Template Method模式中，可以**使用继承改变程序的行为**。这是因为Template Method模式在父类中定义程序行为的框架，在子类中决定具体的处理。
    
    与此相对的是Strategy模式，它可以**使用委托改变程序的行为**。与Template Method模式改变部分程序行为不同的是，Strategy模式用于替换整个算法。
    
## 延伸
* 父类对子类的要求
    * 子类的角度
        * 在子类中可以使用父类中定义的方法
        * 可以通过在子类中增加方法以实现新的功能
        * 在子类中重写父类的方法可以改变程序的行为
    * 父类的角度，声明抽象方法
        * 期待子类去实现抽象方法
        * 要求子类去实现抽象方法
    
        子类具有实现在父类中所声明的抽象方法的责任，这种责任被称为“子类责任（subclass responsibility）。
* 抽象类的意义

    抽象类无法生成其实例。由于在抽象方法中并没有编写具体实现，无法知道在抽象方法中到底进行了什么处理。但是可以决定抽象方法的名字，通过调用使用了抽象方法的模版方法去编写处理。虽然具体的处理内容是由子类决定，不过**在抽象类阶段确定处理流程**非常重要。
    
* 父类与子类间的协作

    将更多方法的实现放在父类中会让子类变得更轻松，但同时将降低子类的灵活性。
    
    如果父类中实现的方法太少，子类就会臃肿，各个子类间代码出现重复。
    
    在Template Method模式中，处理流程被定义在父类中，具体实现交给子类。
    
## 联系实际
