# Singleton模式——单例模式

只有一个实例

* 确保任何情况下都绝对只有1个实例
* 想在程序上表现出“只存在一个实例”

## 示例程序

```Java
    //Singleton.java
    public class Singleton {
        private static Singleton singleton = new Singleton();
        
        private Singleton() {
            System.out.println("生成了一个实例");
        }
        public static Singleton getInstance() {
            return singleton;
        }
    }
    
    //Main.java
    public class Main {
        public static void main(String[] args) {
            System.out.println("Start.");
            
            Singleton obj1 = Singleton.getInstance();
            Singleton obj2 = Singleton.getInstance();
            
            if ( obj1 == obj2 ) {
                System.out.println("obj1 与 obj2 是相同的实例");
            } else {
                System.out.println("obj1 与 obj2 是不同的实例");
            }
            System.out.println("End.")
        }
    }
 ```
    
## 角色
* Singleton

    只有Singleton这一个角色，包含一个返回唯一实例的static方法。
    
## 思路
* 为什么必须设置限制
    
    当存在多个实例时，多个实例相互影响，可能产生意想不到的bug。

* 何时生成这个唯一的实例

    第一次调用getInstance方法时，Singleton类会被初始化。
    
## 相关的设计模式

以下模式中，多数情况下只会生成一个实例。
* AbstractFactory模式
* Builder模式
* Facade模式
* Prototype模式
