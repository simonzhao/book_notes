# Visitor模式 访问者模式

访问数据结构并处理数据

在Visitor模式中，**数据结构与处理被分离开**。

## 示例程序
### Visitor类
### Element接口
### Entry类
### File类
### Directory类
### ListVisitor类
### FileTreatmentException类
### Main类
### Visitor与Element之间的相互调用

## 角色
* Visitor（访问者）
* ConcreteVisitor（具体的访问者）
* Element（元素）
* ConcreteElement
* ObjectStructure（对象结构）

## 拓展思路
### 双重分发
### 为什么要弄得这么复杂
### 开闭原则——对扩展开放，对修改关闭
### 易于增加ConcreteVisitor角色
### 难以增加ConcreteElement角色
### Visitor工作所需的条件

## 相关的设计模式
* Iterator模式
* Composite模式
* Interpreter模式
