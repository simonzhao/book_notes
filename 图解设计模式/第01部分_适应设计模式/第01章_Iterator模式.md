# Iterator模式-迭代器模式

## 示例程序

```Java
    /**
     * 实现一个书架和书籍
     * 可以把书放入书架
     * 获取书架中的全部书籍
     */
    public interface Iterator {
	    public abstract boolean hasNext();
	    public abstract Object next();
    }
    
    public interface Aggregate {
	    public abstract Iterator iterator();
    }
    
    public class Book {
	    private String name;
	    
	    public Book(String name) {
		    this.name = name;
	    }
	    
	    public String getName(){
		    return name;
	    }
    }
    
    public class BookShelf implements Aggregate {
	    private Book[] books;
	    private int last = 0;
	
	    public BookShelf(int maxsize){
		    this.books = new Book[maxsize];
	    }
	
	    public Book getBookAt(int index){
		    return books[index];
	    }
	    
	    public void appendBook(Book book){
		    this.books[last] = book;
		    last++;
	    }
	    
	    public int getLength(){
		    return last;
	    }
	    
	    public Book[] getAll() {
		    reutrn books;
	    }
	
	    public Iterator iterator() {
		    return new BookShelfIterator(this);
	    }
    }
    
    public class BookShelfIterator implements Iterator {
	    private BookShelf bookShelf;
	    private int index;
	    
	    public BookShelf  Iterator( BookShelf bookShelf) {
		    this.bookShelf = bookShelf;
		    this.index = 0;
	    }
	
	    public boolean hasNext() {
		    if (index < bookShelf.getLength()) {
			    return true;
		    } else {
			    return false;
		    }
	    }
	
	    public Object next() {
		    Book book = bookShelf.getBookAt(index);
		    index++;
		    return book;
	    }
    }

    public class Main {
	    public static void main(String[] args) {
		    BookShelf bookShelf = new BookShelf(4);
		    
		    bookShelf .appendBook( new Book("a") );
		    bookShelf .appendBook( new Book("b") );
		    bookShelf .appendBook( new Book("c") );
		    bookShelf .appendBook( new Book("d") );
		
		    Iterator it = bookShelf.iterator();
		    while( it.hasNext() ){ 
			    Book book = (Book)it.next();
			    System.out.println( book.getName());
		    }
	    }
    }
```
## Iterator模式中的角色
* Iterator（迭代器）

    该角色负责定义按顺序逐个遍历元素的接口（API）

    在示例程序中，由Iterator接口扮演这个角色，它定义了hasNext和next两个方法。

    hasNext方法用于判断是否存在下一个元素。

    next方法则用于获取该元素。

* Concretelterator（具体的迭代器）
    
    该角色负责实现Iterator角色所定义的接口（API）。
    
    在示例程序中，由BookShelfIterator类扮演这个角色。该角色包含了遍历集合所必需的信息。

    在示例程序中，BookShelf类的实例保存在bookshelf字段中，被指向的书的下标保存在index字段中。
    
* Aggregate（集合）
    
    该角色负责定义创建Iterator角色的接口（API）。这个接口（API）是一个方法，会创建出”按顺序访问保存在我内部元素的人“。
    
    在示例程序中，有Aggregate接口扮演这个角色，它里面定义了iterator方法。

* ConcreteAggregate（具体的集合）
	
    该角色负责实现Aggregate角色所定义的接口（API）。它会创建出具体的Iterator角色，即ConcreteIterator角色。
    
    在示例程序中，由bookshelf类扮演这个角色，它实现了iterator方法。

## 相关的设计模式
* Visitor模式
	
    Iterator模式是从集合中一个一个的取出元素进行遍历，但是并没有者Iterator接口中声明对取出的元素进行何种处理。

    Visitor模式则是在遍历元素集合的过程中，对元素进行相同的处理。
* Composite模式
    
    Composite模式是具有递归结构的模式，在其中使用Iterator模式比较困难。

* Factory Method模式
    
    在iterator方法中生成Iterator的实例时可能会使用Factory Method模式

## 扩展思路
* 不管实现如何变化，都可以使用Iterator
	
    返回迭代器，屏蔽了ConcreteAggregate角色的具体实现，比如用数组还是容器，调用者不必关心其细节。
    
    ConcreteAggregate的变化，不会导致调用者的跟随变化。

* 难以理解抽象类和接口
	
    难以理解抽象类和接口的人常常使用ConcreteAggregate角色和ConcreteIterator角色编程，而不使用Aggregate接口和Iterator接口，他们总想用具体的类来解决所有问题。

    但是如果只使用具体的类来解决问题，很容易导致类之间的强耦合，这些类也难以作为组件被再次利用。

* Aggregate和Iterator的对应

    Iterator 必须了解 Aggregate的实现细节以及它的API，所以它们是存在对应关系的。
    
* 容易弄错“写一个”
	
    next方法是“返回当前的元素，并指向下一个元素“

* 还容易弄错“最后一个”
    
    hasNext方法是“确认接下来是否可以调用next方法”

* 多个Iterator

    “将遍历功能置于Aggregate角色之外”是Iterator模式的一个特征。

    一个ConcreteAggregate角色可以编写多个ConcreteIterator角色。

* 迭代器的种类多种多样
    
    1. 从前往后
    2. 从后往前
    3. .....
    
* 不需要deleteLterator

    Java会进行垃圾回收，所以不需要进行手动的删除。如果没有GC的话需要程序员手工编写。
