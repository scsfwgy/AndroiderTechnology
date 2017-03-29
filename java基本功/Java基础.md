#Java基础和安卓知识点
##java
####Java基础
*   switch能否用string做参数

	在 Java 7之前，switch 只能支持 byte、short、char、int或者其对应的封装类以及 Enum 类型。在 Java 7中，String支持被加上了。这个新特性是在编译器这个层次上实现的。而在Java虚拟机和字节码这个层次上还是只支持在switch语句中使用与整数类型兼容的类型。这么做的目的就是为了减少这个特性所影响的范围，以降低实现的代价。

*   object有哪些公用方法：equals和==，hashcode，clone，wait，notify等

	1．clone方法
	保护方法，实现对象的浅复制，只有实现了Cloneable接口才可以调用该方法，否则抛出CloneNotSupportedException异常。主要是JAVA里除了8种基本类型传参数是值传递，其他的类对象传参数都是引用传递，我们有时候不希望在方法里讲参数改变，这是就需要在类中复写clone方法。
	2．getClass方法
	final方法，获得运行时类型。
	3．toString方法
	该方法用得比较多，一般子类都有覆盖。
	4．finalize方法
	该方法用于释放资源。因为无法确定该方法什么时候被调用，很少使用。
	5．equals方法
	该方法是非常重要的一个方法。一般equals和==是不一样的，但是在Object中两者是一样的。子类一般都要重写这个方法。
	6．hashCode方法
	该方法用于哈希查找，可以减少在查找中使用equals的次数，重写了equals方法一般都要重写hashCode方法。这个方法在一些具有哈希功能的Collection中用到。一般必须满足obj1.equals(obj2)==true。可以推出obj1.hash- Code()==obj2.hashCode()，但是hashCode相等不一定就满足equals。不过为了提高效率，应该尽量使上面两个条件接近等价。
	如果不重写hashcode(),在HashSet中添加两个equals的对象，会将两个对象都加入进去。
	7．wait方法
	wait方法就是使当前线程等待该对象的锁，当前线程必须是该对象的拥有者，也就是具有该对象的锁。wait()方法一直等待，直到获得锁或者被中断。wait(long timeout)设定一个超时间隔，如果在规定时间内没有获得锁就返回。
	调用该方法后当前线程进入睡眠状态，直到以下事件发生。
	（1）其他线程调用了该对象的notify方法。
	（2）其他线程调用了该对象的notifyAll方法。
	（3）其他线程调用了interrupt中断该线程。
	（4）时间间隔到了。
	此时该线程就可以被调度了，如果是被中断的话就抛出一个InterruptedException异常。
	8．notify方法
	该方法唤醒在该对象上等待的某个线程。	
	9．notifyAll方法	
	该方法唤醒在该对象上等待的所有线程。



*   String、StringBuffer与StringBuilder的区别。

	String 字符串常量
	StringBuilder 字符串变量（非线程安全）
	StringBuffer 字符串变量（线程安全）
		

*   try catch finally，try里有return，finally还执行么？

	结论：
	1、不管有木有出现异常，finally块中代码都会执行；
	2、当try和catch中有return时，finally仍然会执行；
	3、finally是在return后面的表达式运算后执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，管finally中的代码怎么样，返回的值都不会改变，任然是之前保存的值），所以函数返回值是在finally执行前确定的（这一种情况是：finally中没有return）；
	4、finally中最好不要包含return，否则程序会提前退出，返回值不是try或catch中保存的返回值,而是finally中返回的值。

*   Excption与Error包结构。

	![http://img.my.csdn.net/uploads/201310/29/1383051170_4167.jpeg](http://img.my.csdn.net/uploads/201310/29/1383051170_4167.jpeg)

	粉红色的是受检查的异常(checked exceptions),其必须被 try{}catch语句块所捕获,或者在方法签名里通过throws子句声明.受检查的异常必须在编译时被捕捉处理,命名为 CHecked Exception 是因为Java编译器要进行检查,Java虚拟机也要进行检查,以确保这个规则得到遵守.
	绿色的异常是运行时异常(runtime exceptions),需要程序员自己分析代码决定是否捕获和处理,比如 空指针,被0除...
	而声明为Error的，则属于严重错误,需要根据业务信息进行特殊处理,Error不需要捕捉。

*   Override和Overload的含义与区别。

	java中的继承，方法覆盖（重写）override与方法的重载overload的区别 
	方法的重写(Overriding)和重载(Overloading)是Java多态性的不同表现。   
	重写(Overriding)是父类与子类之间多态性的一种表现，而重载(Overloading)是一个类中多态性的一种表现。如果在子类中定义某方法与其父类有相同的名称和参数，我们说该方法被重写 (Overriding) 。子类的对象使用这个方法时，将调用子类中的定义，对它而言，父类中的定义如同被&quot;屏蔽&quot;了。如果在一个类中定义了多个同名的方法，它们或有不同的参数个数或有不同的参数类型或有不同的参数次序，则称为方法的重载(Overloading)。不能通过访问权限、返回类型、抛出的异常进行重载。 
	1. Override 特点   
		1、覆盖的方法的标志必须要和被覆盖的方法的标志完全匹配，才能达到覆盖的效果；   
		2、覆盖的方法的返回值必须和被覆盖的方法的返回一致；   
		3、覆盖的方法所抛出的异常必须和被覆盖方法的所抛出的异常一致，或者是其子类； 
		4、方法被定义为final不能被重写。  
		5、对于继承来说，如果某一方法在父类中是访问权限是private，那么就不能在子类对其进行重写覆盖，如果定义的话，也只是定义了一个新方法，而不会达到重写覆盖的效果。（通常存在于父类和子类之间。） 
			
	2.Overload 特点   
		1、在使用重载时只能通过不同的参数样式。例如，不同的参数类型，不同的参数个数，不同的参数顺序（当然，同一方法内的几个参数类型必须不一样，例如可以是fun(int, float)， 但是不能为fun(int, int)）；   
		2、不能通过访问权限、返回类型、抛出的异常进行重载；   
		3、方法的异常类型和数目不会对重载造成影响；   
		4、重载事件通常发生在同一个类中，不同方法之间的现象。 
		5、存在于同一类中，但是只有虚方法和抽象方法才能被覆写。 
			
	其具体实现机制： 
	overload是重载，重载是一种参数多态机制，即代码通过参数的类型或个数不同而实现的多态机制。是一种静态的绑定机制（在编译时已经知道具体执行的是哪个代码段）。 override是覆盖。覆盖是一种动态绑定的多态机制。即在父类和子类中同名元素（如成员函数）有不同 的实现代码。执行的是哪个代码是根据运行时实际情况而定的。 


*   Interface与abstract类的区别。

	接口和抽象类有什么区别
	你选择使用接口和抽象类的依据是什么？
			
	接口和抽象类的概念不一样。接口是对动作的抽象，抽象类是对根源的抽象。
	抽象类表示的是，这个对象是什么。接口表示的是，这个对象能做什么。比如，男人，女人，这两个类（如果是类的话……），他们的抽象类是人。说明，他们都是人。
	人可以吃东西，狗也可以吃东西，你可以把“吃东西”定义成一个接口，然后让这些类去实现它.
	所以，在高级语言上，一个类只能继承一个类（抽象类）(正如人不可能同时是生物和非生物)，但是可以实现多个接口(吃饭接口、走路接口)。
	第一点． 接口是抽象类的变体，接口中所有的方法都是抽象的。而抽象类是声明方法的存在而不去实现它的类。
	第二点． 接口可以多继承，抽象类不行
	第三点． 接口定义方法，不能实现，而抽象类可以实现部分方法。
	第四点． 接口中基本数据类型为static 而抽类象不是的。
	当你关注一个事物的本质的时候，用抽象类；当你关注一个操作的时候，用接口。
			
	抽象类的功能要远超过接口，但是，定义抽象类的代价高。因为高级语言来说（从实际设计上来说也是）每个类只能继承一个类。在这个类中，你必须继承或编写出其所有子类的
	所有共性。虽然接口在功能上会弱化许多，但是它只是针对一个动作的描述。而且你可以在一个类中同时实现多个接口。在设计阶段会降低难度的。
	
	**JDK8新特性**：在JDK7及以前的版本中，接口中都是抽象方法，不能定义方法体，但是从jdk8开始，接口中可以定义静态的非抽象的方法，直接使用接口名调用静态方法，但是它的实现类的类名或者实例却不可以调用接口中的静态方法。也可以定义普通的非抽象的方法，普通的非抽象方法要在返回值前加上default，对于普通的非抽象方法必须使用子类的实例来调用。如果有多个接口定义了相同的默认方法，实现多个这些接口时必须重写默认方法，否则编译失败。jdk8的接口中，开始允许使用关键字default。

* 面向接口编程与面向对象编程（对上面进行延伸）

		面向接口编程就是面向抽象编程、面向规范编程，它带来的最大的好处便是解耦、增强扩展性、屏蔽变化


*   Static class 与non static class的区别。

		内部静态类不需要有指向外部类的引用；但非静态内部类需要持有对外部类的引用。
		非静态内部类能够访问外部类的静态和非静态成员；静态类不能访问外部类的非静态成员，他只能访问外部类的静态成员。
		一个非静态内部类不能脱离外部类实体被创建，一个非静态内部类可以访问外部类的数据和方法，因为他就在外部类里面。

		【关于内存泄露】内部类持有外部类的引用，或者说内部类对外部类持有隐式的引用。假如我们在内部类中做耗时的操作或者说有个while(true)死循环,即内部类的对象一直存在; 那么当这个MainActivity应该被回收时(比如屏幕的旋转)GC发现它还被其内部类的对象引用 所以就不会将其回收.假如该Activity中含有大量的资源(比如图片)那么就很容易发生OOM. 优化方式:使用静态的内部类,这样的内部类就不会持有外部类的隐式引用;也就不会影响到GC对于外部类的回收。 

			public class A {
			void a1() {
		
			}
			class B{
				void b1() {
					// 可以直接访问
					a1();
				}
			}
		
			static class C {
				void c1() {
					// 不能访问
					// a1();
				}
			}
		
			public static void main(String[] args) {
				A a = new A();
				// 非静态内部类必须依附于外部类
				B b = a.new B();
				C c = new C();
			}
		}
		


*   foreach与正常for循环效率对比。

		需要循环数组结构的数据时，建议使用普通for循环，因为for循环采用下标访问，对于数组结构的数据来说，采用下标访问比较好。
		需要循环链表结构的数据时，一定不要使用普通for循环，这种做法很糟糕，数据量大的时候有可能会导致系统崩溃。
		for在遍历期间可以修改；foreach在遍历期间不可以修改。

		fail-fast 机制是java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。
		　　例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。
		要了解fail-fast机制，我们首先要对ConcurrentModificationException 异常有所了解。当方法检测到对象的并发修改，但不允许这种修改时就抛出该异常。同时需要注意的是，该异常不会始终指出对象已经由不同线程并发修改，如果单线程违反了规则，同样也有可能会抛出改异常。
		诚然，迭代器的快速失败行为无法得到保证，它不能保证一定会出现该错误，但是快速失败操作会尽最大努力抛出ConcurrentModificationException异常，所以因此，为提高此类操作的正确性而编写一个依赖于此异常的程序是错误的做法，正确做法是：ConcurrentModificationException 应该仅用于检测 bug。

*   反射机制

		1、什么是反射机制？
		简单说，反射机制值得是程序在运行时能够获取自身的信息。在java中，只要给定类的名字，那么就可以通过反射机制来获得类的所有信息。
		
		2、java反射机制提供了什么功能？
		在运行时能够判断任意一个对象所属的类
		在运行时构造任意一个类的对象
		在运行时判断任意一个类所具有的成员变量和方法
		在运行时调用任一对象的方法
		在运行时创建新类对象
		
		3、哪里用到反射机制？
		jdbc中有一行代码：Class.forName('com.MySQL.jdbc.Driver.class').newInstance();那个时候只知道生成驱动对象实例，后来才知道，这就是反射，现在
		很多框架都用到反射机制，hibernate，struts都是用反射机制实现的。

		JVM在运行时会产生三个ClassLoader：根装载器、ExtClassLoader（扩展类装载器）和AppClassLoader（系统类装载器）。其中，根装载器不是ClassLoader的子类，它使用C++编写，因此我们在Java中看不到它，根装载器负责装载JRE的核心类库，如JRE目标下的rt.jar、charsets.jar等。ExtClassLoader和AppClassLoader都是ClassLoader的子类。其中ExtClassLoader负责装载JRE扩展目录ext中的JAR类包；AppClassLoader负责装载Classpath路径下的类包。 


*   String类内部实现，能否改变String对象内容，String源码

	参考：String常量池讲解，[http://jefferent.iteye.com/blog/1127799](http://jefferent.iteye.com/blog/1127799)
	参考：jvm内部结构，[http://blog.csdn.net/tonytfjing/article/details/44278233](http://blog.csdn.net/tonytfjing/article/details/44278233)

*   Java1.7与1.8新特性。

	jdk1.7
	1 对集合类的语言支持； 
	2 自动资源管理； 
	3 改进的通用实例创建类型推断； 
	4 数字字面量下划线支持； 
	5 switch中使用string； 
	6 二进制字面量； 
	7 简化可变参数方法调用；
	8 新增一些取环境信息的工具方法；
	9 Boolean类型反转，空指针安全,参与位运算；
	10 两个char间的equals；
	11 安全的加减乘除；
	12 map集合支持并发请求 ，且可以写成 Map map = {name:"xxx",age:18};

	jdk1.8
	接口的默认方法与静态方法
	lambda表达式
	Annotation 多重注解
	java.util.stream支持在流上的函数式风格的值操作

		
####java集合
*  java集合框架的结构
	*  ![](http://img.blog.csdn.net/20160302182650993?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
*  list，set，map接口

	List接口
			
	List接口通常表示一个列表（数组、队列、链表、栈等），其中的元素可以重复，常用实现类为ArrayList和LinkedList，另外还有不常用的Vector。另外，LinkedList还是实现了Queue接口，因此也可以作为队列使用。
			
	Set接口
			
	Set接口通常表示一个集合，其中的元素不允许重复（通过hashcode和equals函数保证），常用实现类有HashSet和TreeSet，HashSet是通过Map中的HashMap实现的，而TreeSet是通过Map中的TreeMap实现的。另外，TreeSet还实现了SortedSet接口，因此是有序的集合（集合中的元素要实现Comparable接口，并覆写Compartor函数才行）。 我们看到，抽象类AbstractCollection、AbstractList和AbstractSet分别实现了Collection、List和Set接口，这就是在Java集合框架中用的很多的适配器设计模式，用这些抽象类去实现接口，在抽象类中实现接口中的若干或全部方法，这样下面的一些类只需直接继承该抽象类，并实现自己需要的方法即可，而不用实现接口中的全部抽象方法。
			
	Map接口
			
	 Map是一个映射接口，其中的每个元素都是一个key-value键值对，同样抽象类AbstractMap通过适配器模式实现了Map接口中的大部分函数，TreeMap、HashMap、WeakHashMap等实现类都通过继承AbstractMap来实现，另外，不常用的HashTable直接实现了Map接口，它和Vector都是JDK1.0就引入的集合类。
			
	Iterator迭代器
			
	Iterator是遍历集合的迭代器（不能遍历Map，只用来遍历Collection），Collection的实现类都实现了iterator()函数，它返回一个Iterator对象，用来遍历集合，ListIterator则专门用来遍历List。而Enumeration则是JDK1.0时引入的，作用与Iterator相同，但它的功能比Iterator要少，它只能再Hashtable、Vector和Stack中使用。


*  哪些是线程安全，哪些不安全

	在集合框架中，有些类是线程安全的，这些都是jdk1.1中的出现的。在jdk1.2之后，就出现许许多多非线程安全的类。 下面是这些线程安全的同步的类：
	vector：就比arraylist多了个同步化机制（线程安全），因为效率较低，现在已经不太建议使用。在web应用中，特别是前台页面，往往效率（页面响应速度）是优先考虑的。
	statck：堆栈类，先进后出
	hashtable：就比hashmap多了个线程安全
	enumeration：枚举，相当于迭代器
	除了这些之外，其他的都是非线程安全的类和接口。

*  hashmap能否用null作为键或值

	HashMap允许key和value为null，而HashTable不允许。
	
*  hashmap源码

	初始容量和加载因子；数组+链表，拉链法；resize():resize函数中新建一个散列表数组，容量为旧表的2倍，接着需要把旧表的键值对迁移到新表。
	线性探测法（步长为1）、线性补偿探测法（步长从1改为Q）、伪随机探测法（步长从1改为随机数）；拉链法（重点）；


*  快速失败与安全失败

	快速失败：当你在迭代一个集合的时候，如果有另一个线程正在修改你正在访问的那个集合时，就会抛出一个ConcurrentModification异常。在java.util包下的都是快速失败。
			
	安全失败：你在迭代的时候会去底层集合做一个拷贝，所以你在修改上层集合的时候是不会受影响的，不会抛出ConcurrentModification异常。在java.util.concurrent包下的全是安全失败的。

*  arraylist，linkedlist底层实现区别，如何扩容
*  treemap，hashmap，linkedhashmap区别和特点，底层实现的区别
	
	* hashmap:数组+链表；快速失败，非线程同步，可以使用ConcurrentHashMap替代实现线程同步与安全失败，或者使用Collections的synchronizedMap方法使HashMap具有同步的能力。
	* linkedhashmap：哈希表与双向链表；快速失败，非线程同步；有序。
	* treemap：红黑树；快速失败，非线程安全；可以自定义排序。

*  如何解决不安全的集合的安全性问题
	*  使用java.util.concurrent相应的安全集合类进行替换使用
	*  使用Collections下的工具类进行同步处理。
####Jvm虚拟机
*  强软弱虚四种引用
*  java内存分区
*  java的个内存分区在什么情况下内存溢出
*  java垃圾回收机制，垃圾收集算法特点及工作在哪一代，分代收集策略，如何判断一个对象该被回收了，java对象实现如何自救，java的垃圾收集器，内存分配与回收及分配担保
*  java的类加载机制，类加载的5个步骤，类加载器

JVM结构、GC工作机制详解：[http://blog.csdn.net/tonytfjing/article/details/44278233](http://blog.csdn.net/tonytfjing/article/details/44278233)
####Java并发
*  线程生命周期

	* 新建（new Thread）当创建Thread类的一个实例（对象）时，此线程进入新建状态（未被启动）。例如：Thread  t1=new Thread();

	* 就绪（runnable）线程已经被启动，正在等待被分配给CPU时间片，也就是说此时线程正在就绪队列中排队等候得到CPU资源。例如：t1.start();

	* 运行（running）线程获得CPU资源正在执行任务（run()方法），此时除非此线程自动放弃CPU资源或者有优先级更高的线程进入，线程将一直运行到结束。

	* 死亡（dead）当线程执行完毕或被其它线程杀死，线程就进入死亡状态，这时线程不可能再进入就绪状态等待执行。自然终止：正常运行run()方法后终止异常终止：调用stop()方法让一个线程终止运行

	* 堵塞（blocked）由于某种原因导致正在运行的线程让出CPU并暂停自己的执行，即进入堵塞状态。正在睡眠：用sleep(long t) 方法可使线程进入睡眠方式。一个睡眠着的线程在指定的时间过去可进入就绪状态。正在等待：调用wait()方法。（调用motify()方法回到就绪状态）被另一个线程所阻塞：调用suspend()方法。（调用resume()方法恢复）

* java如何使用多线程（runnable和Thread）

		实现Runnable接口比继承Thread类所具有的优势：
		
		1）：适合多个相同的程序代码的线程去处理同一个资源
		
		2）：可以避免java中的单继承的限制
		
		3）：增加程序的健壮性，代码可以被多个线程共享，代码和数据独立	

*  stop，resume，suspend的缺点

	一、stop
	stop方法不推荐调用，官方说发是“可能发生不可预测的问题”。其实线程在调用stop方法后，会停止自己。线程停止的时候，会直接停止执行，并释放自己正在使用的锁资源。问题出在这里，如果线程拿到锁，只执行了几步，还剩几步没有执行完，如果此时释放锁，其他线程重新接管，可能导致线程不安全的事情发生。并且这种问题几乎无法Debug。
			
	二、suspend和resume方法
	这两个方法必须要成对出现，否则非常容易发生死锁，因为suspend方法并不会释放锁。如果不能保证之后会有人调用resume方法，会导致线程永远挂起。其次，suspend和resume方法通常不一定是一个线程来顺序执行。有可能一个线程来suspend另一个线程来resume，而如果没有处理好线程间的调用顺序，非常可能发生，resume发生在suspend之前，这样又会导致线程suspend之后，永远没有人来resume他，发生“线程冻结”的场景。


*  终止线程有哪些方法

	1.  使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
    2.  使用stop方法强行终止线程（这个方法不推荐使用，因为stop和suspend、resume一样，也可能发生不可预料的结果）。
    3.  使用interrupt方法中断线程。	
	

*  守护线程

		在Java中有两类线程：User Thread(用户线程)、Daemon Thread(守护线程) 
		用个比较通俗的比如，任何一个守护线程都是整个JVM中所有非守护线程的保姆：
		只要当前JVM实例中尚存在任何一个非守护线程没有结束，守护线程就全部工作；只有当最后一个非守护线程结束时，守护线程随着JVM一同结束工作。
		Daemon的作用是为其他线程的运行提供便利服务，守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者。User和Daemon两者几乎没有区别，唯一的不同之处就在于虚拟机的离开：如果 User Thread已经全部退出运行了，只剩下Daemon Thread存在了，虚拟机也就退出了。 因为没有了被守护者，Daemon也就没有工作可做了，也就没有继续运行程序的必要了。

		(1) thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。
		(2) 在Daemon线程中产生的新线程也是Daemon的。 
		(3) 不要认为所有的应用都可以分配给Daemon来进行服务，比如读写操作或者计算逻辑。 

*  synchronize关键字和wait，notify，notifyAll

		wait()、notify()、notifyAll()是三个定义在Object类里的方法，可以用来控制线程的状态。
		这三个方法最终调用的都是jvm级的native方法。随着jvm运行平台的不同可能有些许差异。
		如果对象调用了wait方法就会使持有该对象的线程把该对象的控制权交出去，然后处于等待状态。
		如果对象调用了notify方法就会通知某个正在等待这个对象的控制权的线程可以继续运行。
		如果对象调用了notifyAll方法就会通知所有等待这个对象控制权的线程继续运行。
		比较难懂，参看这篇文章：
		[http://blog.csdn.net/luoweifu/article/details/46664809](http://blog.csdn.net/luoweifu/article/details/46664809)	

*  lock与condition，Java中的几种不同锁
	*  volatile 只保证可见性，不保证原子性！
	*  synchronized 方法锁、代码块锁；原子性、可见性
	*  ReentrantLock 效果同synchronized，有增强，必须保证unlock。【排它锁】
	*  CountDownLatch：共享锁
	*  ReentrantReadWriteLock  多个Thread可以同时进行读取操作，但是同一时刻只允许一个Thread进行写入操作。【读：共享锁；写：排它锁】
	*  Condition 线程间通信，Condition可 以替代传统的线程间通信，用await()替换wait()，用signal()替换notify()，用signalAll()替换notifyAll()。
	*  详情参看：[http://blog.csdn.net/vking_wang/article/details/9952063](http://blog.csdn.net/vking_wang/article/details/9952063)

*  线程死锁情景

		死锁形成的原因：
		系统资源不足
		进程（线程）推进的顺序不恰当；
		资源分配不当
		
		锁形成的条件：
		互斥条件：所谓互斥就是进程在某一时间内独占资源。
		请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
		不剥夺条件：进程已获得资源，在末使用完之前，不能强行剥夺。
		循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。
		
		从编程经验上来讲，形成死锁的一般原因有以下几种：
		个人使用锁的经验差异。
		程序模块使用锁的差异。
		工程代码版本之间的差异。
		工程代码分支之间的差异。
		修改代码和重构代码带来的差异。
		
		死锁的代价是非常大的，有时候很难检测排查，因此需要在编程过程中尽可能的避免发生死锁。编程中为了避免死锁应该遵循如下策略：
		在编写多线程程序之前，首先编写正确的程序，然后再移植到多线程。
		时刻检查自己写的程序有没有在跳出时忘记释放锁。
		如果自己的模块可能重复使用一个锁，建议使用嵌套锁。
		对于某些锁代码，不要临时重新编写，建议使用库里面的锁，或者自己曾经编写的锁。
		如果某项业务需要获取多个锁，必须保证锁的按某种顺序获取，否则必定死锁。
		编写简单的测试用例，验证有没有死锁。
		编写验证死锁的程序，从源头避免死锁。



*  消费者生产者模型

	类似于观察者模式，只多了一个消息队列。
	参看：[http://blog.csdn.net/luohuacanyue/article/details/14648185](http://blog.csdn.net/luohuacanyue/article/details/14648185)，并没看太懂。

* volatile关键字，是否保证原子性，优缺点

		volatile 只保证可见性，不保证原子性！
		对volatile变量所有的写操作都能立刻被其他线程得知。但是这并不代表基于volatile变量的运算在并发下是安全的，因为volatile只能保证内存可见性，却没有保证对变量操作的原子性。

*  ThreadLocal的特点和使用

		ThreadLocal 不是用于解决共享变量的问题的，不是为了协调线程同步而存在，而是为了方便每个线程处理自己的状态而引入的一个机制，理解这点对正确使用ThreadLocal至关重要。
		[https://my.oschina.net/clopopo/blog/149368](https://my.oschina.net/clopopo/blog/149368)

*  单例模式和多线程

		* 双重锁单例模式也解决不了反射创建多个实例的问题。
			
			枚举类型的单例模式
			
			public class EnumSingleton {
				private EnumSingleton() {
				}
			
				public static EnumSingleton getInstance() {
					return Singleton.INSTANCE.getInstance();
				}
			
				private static enum Singleton {
					INSTANCE;
			
					private EnumSingleton singleton;
			
					// JVM会保证此方法绝对只调用一次
					private Singleton() {
						singleton = new EnumSingleton();
					}
			
					public EnumSingleton getInstance() {
						return singleton;
					}
				}
			
				public static void main(String[] args) {
					EnumSingleton obj1 = EnumSingleton.getInstance();
					EnumSingleton obj2 = EnumSingleton.getInstance();
					// 输出结果：obj1==obj2?true
					System.out.println("obj1==obj2?" + (obj1 == obj2));
				}
			}


*  SimpleDateFormat的安全性问题
	
	[http://www.cnblogs.com/zemliu/archive/2013/08/29/3290585.html](http://www.cnblogs.com/zemliu/archive/2013/08/29/3290585.html)

*  java的并发容器包Concurrent。阻塞队列，CopyOnWriteList等
	* Concurrent：Java 5 添加了一个新的包到 Java 平台，java.util.concurrent 包。这个包包含有一系列能够让 Java 的并发编程变得更加简单轻松的类。在这个包被添加以前，你需要自己去动手实现自己的相关工具类。
	* BlockingQueue：队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列；当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒。所谓的生产者-消费者模式就是借助这个东西进行数据交互的。
	* CopyOnWriteList：读写分离，CopyOnWrite容器即写时复制的容器。通俗的理解是当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。缺点：内存占用问题；数据一致性问题（只能保证最终一致，不能保证实时一致）。
*  ConcurrentHashMap的源理
![](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)

	左边便是Hashtable的实现方式---锁整个hash表；而右边则是ConcurrentHashMap的实现方式---锁桶（或段）。ConcurrentHashMap将hash表分为16个桶（默认值），诸如get,put,remove等常用操作只锁当前需要用到的桶。试想，原来只能一个线程进入，现在却能同时16个写线程进入（写线程才需要锁定，而读线程几乎不受限制，之后会提到），并发性的提升是显而易见的。
	更令人惊讶的是ConcurrentHashMap的读取并发，因为在读取的大多数时候都没有用到锁定，所以读取操作几乎是完全的并发操作，而写操作锁定的粒度又非常细，比起之前又更加快速（这一点在桶更多时表现得更明显些）。只有在求size等操作时才需要锁定整个表。而在迭代时，ConcurrentHashMap使用了不同于传统集合的快速失败迭代器（见之前的文章《JAVA API备忘---集合》）的另一种迭代方式，我们称为弱一致迭代器。在这种迭代方式中，当iterator被创建后集合再发生改变就不再是抛出ConcurrentModificationException，取而代之的是在改变时new新的数据从而不影响原有的数据，iterator完成后再将头指针替换为新的数据，这样iterator线程可以使用原来老的数据，而写线程也可以并发的完成改变，更重要的，这保证了多个线程并发执行的连续性和扩展性，是性能提升的关键。

	see:[http://blog.csdn.net/liuzhengkang/article/details/2916620](http://blog.csdn.net/liuzhengkang/article/details/2916620)

*  java的线程池原理和自带的四大线程池


	Java通过Executors提供四种线程池，分别为：
	
	newCachedThreadPool创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
	newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
	newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
	newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

*  Executor框架

* 线程间通信【重点】


	当线程调用了wait()方法时，它会释放掉对象的锁。
	另一个会导致线程暂停的方法：Thread.sleep()，它会导致线程睡眠指定的毫秒数，但线程在睡眠的过程中是不会释放掉对象的锁的。

	* 多线程间通信方式：
	1、共享变量
	2、wait/notify机制
	3、Lock/Condition机制
	4、管道
	
	* 进程与进程间通信
	（1）管道（Pipe）：管道可用于具有亲缘关系进程间的通信，允许一个进程和另一个与它有共同祖先的进程之间进行通信。
	（2）命名管道（named pipe）：命名管道克服了管道没有名字的限制，因此，除具有管道所具有的功能外，它还允许无亲缘关 系 进程间的通信。命名管道在文件系统中有对应的文件名。命名管道通过命令mkfifo或系统调用mkfifo来创建。
	（3）信号（Signal）：信号是比较复杂的通信方式，用于通知接受进程有某种事件发生，除了用于进程间通信外，进程还可以发送 信号给进程本身；Linux除了支持Unix早期信号语义函数sigal外，还支持语义符合Posix.1标准的信号函数sigaction（实际上，该函数是基于BSD的，BSD为了实现可靠信号机制，又能够统一对外接口，用sigaction函数重新实现了signal函数）。
	（4）消息（Message）队列：消息队列是消息的链接表，包括Posix消息队列system V消息队列。有足够权限的进程可以向队列中添加消息，被赋予读权限的进程则可以读走队列中的消息。消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺
	（5）共享内存：使得多个进程可以访问同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。往往与其它通信机制，如信号量结合使用，来达到进程间的同步及互斥。
	（6）内存映射（mapped memory）：内存映射允许任何多个进程间通信，每一个使用该机制的进程通过把一个共享的文件映射到自己的进程地址空间来实现它。
	（7）信号量（semaphore）：主要作为进程间以及同一进程不同线程之间的同步手段。
	（8）套接口（Socket）：更为一般的进程间通信机制，可用于不同机器之间的进程间通信。起初是由Unix系统的BSD分支开发出来的，但现在一般可以移植到其它类Unix系统上：Linux和System V的变种都支持套接字。

	




##Android
####Android基础
*  五种布局的特点和效率对比
*  Activity的生命周期和缓存

![](http://hi.csdn.net/attachment/201109/1/0_1314838777He6C.gif)

		有a、b两个activity，当a进入b之后的一段时间，可能系统就把a回收了，这时候按back键，执行的不是a的onStart()方法，而是onCreate()方法，a被重新创建了一次，这时a的临时数据和状态就很有可能就丢失了。而Activity中的onSaveInstanceState()回调方法会保存临时的数据和状态，这个方法一定会再activity回收之前调用。方法中有一个Bundle参数，putString()、putInt()等方法需要传入两个参数，一个键一个值。数据保存之后会在onCreate中会恢复，onCreate也有一个Bundle类型的参数。
		关键字：onSaveInstanceState(Bundle outState),onCreate(Bundle savedInstanceState),onRestoreInstanceState(Bundle outState)。
		see:[http://blog.csdn.net/lccly/article/details/6089667](http://blog.csdn.net/lccly/article/details/6089667)

*  Fragment的生命周期

![](http://img.my.csdn.net/uploads/201211/29/1354170682_3824.png)
![](http://img.blog.csdn.net/20150310094648441)

*  Service的两种启动方式，如何保证一个Service不被杀死
	* Context.startService() 
	* Context.bindService(intent, conn, BIND_AUTO_CREATE);
	* 如何保证一个Service不被杀死:
		* [http://blog.csdn.net/mad1989/article/details/22492519](http://blog.csdn.net/mad1989/article/details/22492519)
		* onStartCommand方法，返回START_STICKY，手动返回START_STICKY，亲测当service因内存不足被kill，当内存又有的时候，service又被重新创建，比较不错，但是不能保证任何情况下都被重建，比如进程被干掉了....
		* 提升service优先级，android:priority = "1000"（可能无效）
		* 提升service进程优先级， startForeground(0x111, notification)。用户体验不好，通知栏会看到。如果在极度极度低内存的压力下，该service还是会被kill掉，并且不一定会restart
		* onDestroy方法里重启service，当使用类似口口管家等第三方应用或是在setting里-应用-强制停止时，APP进程可能就直接被干掉了，onDestroy方法都进不来，所以还是无法保证~.~
		* Application加上Persistent属性，据说这个属性不能乱设置，不过设置后，的确发现优先级提高不少，或许是相当于系统级的进程，但是还是无法保证存活
*  广播注册的两种方式的区别
*  如何安全的退出一个已经开启多个activity的APP
*  Android的进程优先级
*  Asset目录与res目录的区别
*  ANR问题，force close问题，activity，service，broadcast各自超过多少秒报ANR
*  Activity启动模式，intent匹配规则
*  Android反编译
*  JNI和native方法
*  Android系统的有哪些安全机制
*  Android新特性有哪些（5.0 6.0 7.0）
*  Dalvik和ART区别
*  Android屏幕适配有哪些方法
####Android View相关
*  View事件体系，滑动冲突
*  View绘制原理，性能问题（view绘制间隔16ms，60fps）,measure，layout，draw三大过程
*  如何避免overdraw
*  自定义View相关知识，下拉刷新的实现
*  Android动画分类和各自特点
*  View和SurfaceView的区别
####Android性能优化
*  APK瘦身的办法
*  ListView的源码和如何优化Listview
*  Android如何获取Crash信息
*  ANR日志分析
*  Android动态加载，如何解决方法数越界
*  Android怎么加速启动Activity
*  Android内存泄漏问题，内存泄漏分析工具
*  Android内存溢出问题，图片的三级缓存
####Android framework和底层原理
*  Android系统开机过程
*  Zygote和System进程的启动过程
*  Activity的启动过程
*  Android的消息机制，Handler原理
*  AsyncTask源码，优缺点
*  HandlerThread和IntentService的原理和特点
* Android的IPC机制
*  AIDL原理
* Binder机制原理
*  ActivityManagerService
####Android框架和源码
*  项目各个细节熟练，熟悉项目用到的框架源码
*  Volley
*  xUtils
*  okHttp
*  UniversalImageLoader
*  RxJava