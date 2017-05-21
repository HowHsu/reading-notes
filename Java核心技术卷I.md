## Chapter 6 接口、lambda表达式与内部类

### 6.1接口

#### 6.1.3 接口与抽象类

由于Java只允许每个类只继承（extends）于一个类。考虑到想要提供多重继承的好处同时还避免多重继承的复杂性，Java引入了接口的概念。

	class Employees extends Person, Comparable //Error
	class Employees extends Person implements Comparable //OK


#### 6.1.4 静态方法

1）实现方式：在接口中实现静态方法。

2）解决的问题：用于完成更多的操作而无须再另外实现一个伴随函数。

3）引入的问题：这有违将接口作为抽象规范的初衷。

4）建议：通常的做法是将静态方法放在伴随类中。在标准库中可以看到有成对出现的接口，如Collection/Collections或Path/Paths。

#### 6.1.5 默认方法

1）实现方式：在接口方法前加default修饰。

2）解决的问题：

 1. 可避免当为接口添加新方法时，已有的实现该接口的类由于没有实现该新方法而导致编译不通过。
 2. 如果使用了之前的包含该类的JAR文件，则类能正常加载，但当该类的实例调用接口的新方法时会出现AbstractMethodError错误。

3）引入的问题：默认方法冲突。

- 冲突的种类：

 1. 当一个类继承（extends）了一个超类，同时实现了一个接口，并从超类和接口继承类相同的方法。
 2. 当一个类实现了两个接口，且这两个接口有使用默认方法，则产生冲突。若都没有默认方法，则不冲突。

- 冲突的对策：

 1. 超类优先。如果超类提供了一个具体方法，同名且有相同参数类型的默认方法将被忽略。
 2. 接口冲突。必须通过实现这两个方法中的一个来消除二义性，从而消除冲突。


 

-----------------------


## Chapter 9 集合

### 9.1 Java集合框架

#### 9.1.1 将集合的接口与实现分离

1. 接口与实现分离
2. ArrayDeque：循环数组队列 LinkedList：链表队列
3. AbstractQueue：为类库实现者设计，如果想要实现自己的队列类可以扩展该类。

#### 9.1.2 Collection接口

集合类的基本接口是Collection接口，其有2个基本方法：

	public interface Collection<E>
	{
	    boolean add(E element);
	    Iterator<E> iterator();
	    . . .
	}

① add向集合添加元素。添加元素改变了集合返回true，若集合没改变就返回false。（保证去重）  
② iterator方法用于返回一个实现了Iterator接口的对象。

#### 9.1.3 迭代器

Iterator接口包含4个方法：

	public interface Iterator<E>
	{
		E next();
	    boolean hasNext();
		void remove();
	    default void forEachRemaining(Consumer<?  super E> action);
	    . . .
	}

##### ① 遍历

	Collection<String> c=. . .;

1）在调用next之前调用hasNext方法，如此反复。

	Iterator<String> iter = c.iterator();
	while(iter.hasNext())
	{
	    String element = iter.next();
	    do sometiong with element...
	}
2）用“for each”循环。

	for(String element : c)
	{
	    String elemnt = iter.next();
	    do something with element...
	}

**“for each”循环可以与任何实现了Iterable接口的对象一起工作。**
Iterable接口只包含1个抽象方法：

	public interface Iterable<E>
	{
	    Iterator<E> iterator();
	    . . .
	}
Collection接口扩展了Iterable接口。因此，**对于标准类库中的任何集合都可以使用“for each”循环。**  

遍历时元素被访问顺序取决于集合类型：对于ArrayList，索引从0开始自增。对于HashSet，顺序随机。

##### ② 和C++的区别

C++中，迭代器是根据数组索引建模的，**可以通过a[i]的方式查找元素而不用移动位置，也可以通过i++方式直接移动位置而不查找（访问）元素**。  
Java中**查找操作与位置变更紧密是相连的**。查找一个元素唯一的方法是调用next，而在执行查找的同时，位置向前移动。

##### ③ 删除

Iterator接口的remove方法将会删除上次调用next方法时返回的元素，这意味着要先调用next然后再执行remove删除。即访问（查找）过以后再删除。

	Iterator<String> it = c.iterator();
	it.next();	//先跳过第一个元素
	it.remove();//再执行删除

如果在调用remove前没有调用next方法是不合法的。
如果想要删除相邻两个元素，则需要在删除第二个之前调用一次next。

	it.remove();
	it.next();
	it.remove();

