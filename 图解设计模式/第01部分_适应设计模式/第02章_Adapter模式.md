# Adapter模式 适配器模式

加个“适配器”以便于复用

## 介绍

现有的程序无法直接使用，需要做适当的变换之后才能使用。用于填补“现有的程序”和“所需的程序”之间差异的设计模式就是**Adapter模式**。

### Apadter模式有以下两种：
* 类适配器模式（使用继承的适配器）
* 对象适配器模式（使用委托的适配器）

#### 类适配器模式示例程序
    pulbic class Banner {
        private String string;
        
        public Banner(String string){
            this.string = string;
        }
        
        public void showWithParen() {
            System.out.println("(" + string + ")");
        }
        
        public void showWithAster() {
            System.out.println("*" + string + "*");
        }
    }
    
    public interface Print {
        public abstract void printWeak();
        public abstract void printStrong();
    }
    
    public class PrintBanner extends Banner implements Print {
        public PrintBanner( String string ) {
            super( string );
        }
        
        public void printWeak() {
            showWithParen();
        }
        
        public void printStrong() {
            showWithAster();
        }
    }
    
    public class Main {
        public static void main( String[] args ) {
            Print p = new PrintBanner("Hello");
            p.printWeak();//(Hello)
            p.printStrong();//*Hello*
        }
    }
    
#### 对象适配器模式示例程序
    public abstract class Print {
        public abstract void printWeak();
        public abstract void printStrong();
    }
    
    public class PrintBanner extends Print {
        private Banner banner;
        
        public PrintBanner( String string ) {
            this.banner = new Banner( string );
        }
        
        public void printWeak() {
            banner.showWithParen();
        }
        
        public void printStrong() {
            banner.showWithAster();
        }
    }
    
### Adapter模式中的角色
* Target（对象）

    负责定义所需要的方法。示例程序中的Print（接口|类）扮演。
    
* Client（请求者）

    使用Target角色所定义的方法进行具体处理。示例程序中的Main扮演。
    
* Adaptee（被适配）

    持有既定方法的角色，示例程序中由Banner类扮演。
    
* Adapter（适配）

    使用Adaptee角色的方法，满足Target角色的需求。示例程序中由PrintBanner类扮演。
    
    在类适配器模式中，Adapter角色通过继承来使用Adaptee角色；在对象适配器模式中，Adapter通过委托来使用Adaptee角色。
    
### 扩展思路
* 什么时候使用Adapter模式

    如果某个方法就是我们所需要的方法，那么直接在程序中使用不就可以了吗？为什么要考虑使用Adapter模式呢？
    
    很多时候我会用到现有的类，Adapter模式会对现有的类进行适配，生成新的类，当出现Bug时，只需要检查Adapter角色的类就可以了。
    
* 如果没有现成的代码

    使用Adapter模式可以在完全不改变现有代码的前提下使现有代码适配新的接口（API）
    
* 版本升级与兼容性

    新版本扮演Adaptee角色，旧版本扮演Target角色，编写一个扮演Adapter角色的类，解决版本兼容问题。
    
* 功能完全不同的类
    
    当Adaptee角色和Target角色的功能完全不同时，Adapter模式是无法使用的。

### 相关的设计模式
* Bridge模式

    Adapter模式用于连接接口（API）不同的类，而Bridge模式则用于连接类的功能层次结构与实现层次结构。
    
* Decorator模式
    
    Adapter模式用于填补不同接口（API）之间的缝隙，而Decorator模式则是在不改变接口（API）的前提下增加功能。

## 与现实结合
