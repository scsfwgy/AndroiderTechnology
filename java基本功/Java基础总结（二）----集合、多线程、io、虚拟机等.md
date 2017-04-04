## Java集合
#### java集合框架的结构
![](http://img.blog.csdn.net/20160302182650993?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

#### List接口
List接口通常表示一个列表（数组、队列、链表、栈等），其中的元素可以重复，常用实现类为ArrayList和LinkedList，另外还有不常用的Vector。另外，LinkedList还是实现了Queue接口，因此也可以作为队列使用。
			
#### Set接口
Set接口通常表示一个集合，其中的元素不允许重复（通过hashcode和equals函数保证），常用实现类有HashSet和TreeSet，HashSet是通过Map中的HashMap实现的，而TreeSet是通过Map中的TreeMap实现的。另外，TreeSet还实现了SortedSet接口，因此是有序的集合（集合中的元素要实现Comparable接口，并覆写Compartor函数才行）。 我们看到，抽象类AbstractCollection、AbstractList和AbstractSet分别实现了Collection、List和Set接口，这就是在Java集合框架中用的很多的适配器设计模式（这应该是模板模式吧？**求确认**，或者说只是继承而已。怎么能扯到适配器），用这些抽象类去实现接口，在抽象类中实现接口中的若干或全部方法，这样下面的一些类只需直接继承该抽象类，并实现自己需要的方法即可，而不用实现接口中的全部抽象方法。
			
#### Map接口
 Map是一个映射接口，其中的每个元素都是一个key-value键值对，同样抽象类AbstractMap通过适配器模式实现了Map接口中的大部分函数，TreeMap、HashMap、WeakHashMap等实现类都通过继承AbstractMap来实现，另外，不常用的HashTable直接实现了Map接口，它和Vector都是JDK1.0就引入的集合类。
			
#### Iterator迭代器
Iterator是遍历集合的迭代器（不能遍历Map，只用来遍历Collection），Collection的实现类都实现了iterator()函数，它返回一个Iterator对象，用来遍历集合，ListIterator则专门用来遍历List。而Enumeration则是JDK1.0时引入的，作用与Iterator相同，但它的功能比Iterator要少，它只能在Hashtable、Vector和Stack中使用。

#### 哪些是线程安全，哪些不安全
* 在集合框架中，有些类是线程安全的，这些都是jdk1.1中的出现的。在jdk1.2之后，就出现许许多多非线程安全的类。 下面是这些线程安全的同步的类：
* vector：就比arraylist多了个同步化机制（线程安全），因为效率较低，现在已经不太建议使用。在web应用中，特别是前台页面，往往效率（页面响应速度）是优先考虑的。
* statck：堆栈类，先进后出。
* hashtable：就比hashmap多了个线程安全。加锁时机在方法上，不建议使用。可以考虑用CurrentHashMap替代。
* enumeration：枚举，相当于迭代器

#### hashmap
* hashmap能否用null作为键或值
HashMap允许key和value为null，而Hashtable不允许。
	
*  hashmap源码
1. 源码阅读：TODO:请推荐一篇好的分析文章。
2. 关键点：
	1. 初始容量和加载因子；
	2. 数组+链表，拉链法；
	3. resize():resize函数中新建一个散列表数组，容量为旧表的2倍，接着需要把旧表的键值对迁移到新表。
3. 数据定位的方式总结：线性探测法（步长为1）、线性补偿探测法（步长从1改为Q）、伪随机探测法（步长从1改为随机数）；拉链法（重点）；


####  快速失败与安全失败
快速失败：当你在迭代一个集合的时候，如果有另一个线程正在修改你正在访问的那个集合时，就会抛出一个ConcurrentModification异常。在java.util包下的都是快速失败。
安全失败：你在迭代的时候会去底层集合做一个拷贝，所以你在修改上层集合的时候是不会受影响的，不会抛出ConcurrentModification异常。在java.util.concurrent包下的全是安全失败的。

####  arraylist，linkedlist底层实现区别，如何扩容
* arraylist底层是数组，linkedlist底层是双向链表。
* arraylist扩容到原来的1.5倍`int newCapacity = oldCapacity + (oldCapacity >> 1);`，然后把旧的数据copy到新的数组中。linedList更简单，直接在原来链表上加一个新的指针，指向上一个指针即可。特别注意和hashmap扩容对比，hashmap扩容为原来的2倍，并且在达到0.75即开始扩容，原因：为了增加命中的概率，如果太满的话，可能导致数组+链表的结构中，链表太长，导致查询慢。


####  treemap，hashmap，linkedhashmap区别和特点，底层实现的区别
* hashmap:数组+链表；快速失败，非线程同步，可以使用ConcurrentHashMap替代实现线程同步与安全失败，或者使用Collections的synchronizedMap方法使HashMap具有同步的能力。特别注意：在jdk1.8中对于链表过长情况下，或转换为红黑树提高查询效率。
* linkedhashmap：哈希表与双向链表；快速失败，非线程同步；有序。
* treemap：红黑树；快速失败，非线程安全；可以自定义排序。
*  如何解决不安全的集合的安全性问题
	*  使用java.util.concurrent相应的安全集合类进行替换使用
	*  使用Collections下的工具类进行同步处理。

##多线程和并发包
#### 线程生命周期
* 新建（new Thread）当创建Thread类的一个实例（对象）时，此线程进入新建状态（未被启动）。例如：Thread  t1=new Thread();
* 就绪（runnable）线程已经被启动，正在等待被分配给CPU时间片，也就是说此时线程正在就绪队列中排队等候得到CPU资源。例如：t1.start();
* 运行（running）线程获得CPU资源正在执行任务（run()方法），此时除非此线程自动放弃CPU资源或者有优先级更高的线程进入，线程将一直运行到结束。
* 死亡（dead）当线程执行完毕或被其它线程杀死，线程就进入死亡状态，这时线程不可能再进入就绪状态等待执行。自然终止：正常运行run()方法后终止异常终止：调用stop()方法让一个线程终止运行
* 堵塞（blocked）由于某种原因导致正在运行的线程让出CPU并暂停自己的执行，即进入堵塞状态。正在睡眠：用sleep(long t) 方法可使线程进入睡眠方式。一个睡眠着的线程在指定的时间过去可进入就绪状态。正在等待：调用wait()方法。（调用motify()方法回到就绪状态）被另一个线程所阻塞：调用suspend()方法。（调用resume()方法恢复）

#### java如何使用多线程（runnable和Thread）,实现Runnable接口比继承Thread类所具有的优势	
1. 适合多个相同的程序代码的线程去处理同一个资源	
2. 可以避免java中的单继承的限制		
3. 增加程序的健壮性，代码可以被多个线程共享，代码和数据独立	

#### stop，resume，suspend的缺点

1. stop
stop方法不推荐调用，官方说发是“可能发生不可预测的问题”。其实线程在调用stop方法后，会停止自己。线程停止的时候，会直接停止执行，并释放自己正在使用的锁资源。问题出在这里，如果线程拿到锁，只执行了几步，还剩几步没有执行完，如果此时释放锁，其他线程重新接管，可能导致线程不安全的事情发生。并且这种问题几乎无法Debug。
			
2. suspend和resume方法
这两个方法必须要成对出现，否则非常容易发生死锁，因为suspend方法并不会释放锁。如果不能保证之后会有人调用resume方法，会导致线程永远挂起。其次，suspend和resume方法通常不一定是一个线程来顺序执行。有可能一个线程来suspend另一个线程来resume，而如果没有处理好线程间的调用顺序，非常可能发生，resume发生在suspend之前，这样又会导致线程suspend之后，永远没有人来resume他，发生“线程冻结”的场景。

#### 终止线程有哪些方法
1.  使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
2.  使用stop方法强行终止线程（这个方法不推荐使用，因为stop和suspend、resume一样，也可能发生不可预料的结果）。
3.  使用interrupt方法中断线程。	
	
#### 守护线程
* 在Java中有两类线程：User Thread(用户线程)、Daemon Thread(守护线程) 
* 用个比较通俗的比如，任何一个守护线程都是整个JVM中所有非守护线程的保姆：
只要当前JVM实例中尚存在任何一个非守护线程没有结束，守护线程就全部工作；只有当最后一个非守护线程结束时，守护线程随着JVM一同结束工作。
* Daemon的作用是为其他线程的运行提供便利服务，守护线程最典型的应用就是 GC (垃圾回收器)，它就是一个很称职的守护者。User和Daemon两者几乎没有区别，唯一的不同之处就在于虚拟机的离开：如果 User Thread已经全部退出运行了，只剩下Daemon Thread存在了，虚拟机也就退出了。 因为没有了被守护者，Daemon也就没有工作可做了，也就没有继续运行程序的必要了。
(1) thread.setDaemon(true)必须在thread.start()之前设置，否则会跑出一个IllegalThreadStateException异常。你不能把正在运行的常规线程设置为守护线程。
(2) 在Daemon线程中产生的新线程也是Daemon的。 
(3) 不要认为所有的应用都可以分配给Daemon来进行服务，比如读写操作或者计算逻辑。 

#### wait，notify，notifyAll关键字
* wait()、notify()、notifyAll()是三个定义在Object类里的方法，可以用来控制线程的状态。这三个方法最终调用的都是jvm级的native方法。随着jvm运行平台的不同可能有些许差异。如果对象调用了wait方法就会使持有该对象的线程把该对象的控制权交出去，然后处于等待状态。如果对象调用了notify方法就会通知某个正在等待这个对象的控制权的线程可以继续运行。如果对象调用了notifyAll方法就会通知所有等待这个对象控制权的线程继续运行。最原始的线程间通过新的方式。
* @see[http://blog.csdn.net/luoweifu/article/details/46664809](http://blog.csdn.net/luoweifu/article/details/46664809)	

#### lock与condition，Java中的几种不同锁
* volatile 只保证可见性，不保证原子性。可见性：总是保持数据的为最新的，虚拟机不做任何优化处理。不会将该变量上的操作与其他内存操作一起重排序。volatile变量不会被缓存在寄存器或者对其他处理器不可见的地方，因此在读取volatile类型的变量时总会返回最新写入的值。
* synchronized 方法锁、代码块锁；保证：原子性、可见性。
* ReentrantLock 效果同synchronized，必须保证unlock。新增：锁投票，定时锁等候和中断锁等候【排它锁】
* CountDownLatch：共享锁（读锁）
* ReentrantReadWriteLock  多个Thread可以同时进行读取操作，但是同一时刻只允许一个Thread进行写入操作。【读：共享锁；写：排它锁】
* Condition 线程间通信，Condition可 以替代传统的线程间通信，用await()替换wait()，用signal()替换notify()，用signalAll()替换notifyAll()。
* @see：[http://blog.csdn.net/vking_wang/article/details/9952063](http://blog.csdn.net/vking_wang/article/details/9952063)

#### 线程死锁情景
* 死锁形成的原因：
	1. 系统资源不足
	2. 进程（线程）推进的顺序不恰当；
	3. 资源分配不当
		
* 锁形成的条件：
	1. 互斥条件：所谓互斥就是进程在某一时间内独占资源。
	2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
	3. 不剥夺条件：进程已获得资源，在末使用完之前，不能强行剥夺。
		
* 循环等待条件：
	1. 若干进程之间形成一种头尾相接的循环等待资源关系。
		
* 从编程经验上来讲，形成死锁的一般原因有以下几种：
	1. 个人使用锁的经验差异。
	2. 程序模块使用锁的差异。
	3. 工程代码版本之间的差异。
	4. 工程代码分支之间的差异。
	5. 修改代码和重构代码带来的差异。
		
* 死锁的代价是非常大的，有时候很难检测排查，因此需要在编程过程中尽可能的避免发生死锁。编程中为了避免死锁应该遵循如下策略：
	1. 在编写多线程程序之前，首先编写正确的程序，然后再移植到多线程。
	2. 时刻检查自己写的程序有没有在跳出时忘记释放锁。
	3. 如果自己的模块可能重复使用一个锁，建议使用嵌套锁。 对于某些锁代码，不要临时重新编写，建议使用库里面的锁，或者自己曾经编写的锁。 如果某项业务需要获取多个锁，必须保证锁的按某种顺序获取，否则必定死锁。
	4. 写简单的测试用例，验证有没有死锁。
	5. 编写验证死锁的程序，从源头避免死锁。



#### 消费者生产者模型
@see:[http://blog.csdn.net/autumn20080101/article/details/9491159](http://blog.csdn.net/autumn20080101/article/details/9491159)
@see：[http://blog.csdn.net/monkey_d_meng/article/details/6251879](http://blog.csdn.net/monkey_d_meng/article/details/6251879)

#### volatile关键字，是否保证原子性，优缺点
volatile 只保证可见性，不保证原子性！对volatile变量所有的写操作都能立刻被其他线程得知。但是这并不代表基于volatile变量的运算在并发下是安全的，因为volatile只能保证内存可见性，却没有保证对变量操作的原子性。

#### Java transient关键字
Java的serialization提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它。为了在一个特定对象的一个域上关闭serialization，可以在这个域前加上关键字transient。transient是Java语言的关键字，用来表示一个域不是该对象串行化的一部分。当一个对象被串行化的时候，transient型变量的值不包括在串行化的表示中，然而非transient型的变量是被包括进去的。注意static变量也是可以串行化的

#### ThreadLocal的特点和使用
* ThreadLocal 和synchronized的策略刚好相反，ThreadLocal通过对操作的对象保存一份副本，任何操作都会只是针对副本，所以不用加任何锁就可以实现synchronized的效果，以空间换时间。
* 有一个问题：一个线程修改了Thread包装的对象，如何及时和原有对象数据保持同步？因为修改的是副本。还是说线程之间操作数据（主要指修改）互不影响？
* @see：[http://blog.csdn.net/huachao1001/article/details/51970237](http://blog.csdn.net/huachao1001/article/details/51970237)
* @see[https://my.oschina.net/clopopo/blog/149368](https://my.oschina.net/clopopo/blog/149368)

#### SimpleDateFormat的安全性问题
@see:[http://www.cnblogs.com/zemliu/archive/2013/08/29/3290585.html](http://www.cnblogs.com/zemliu/archive/2013/08/29/3290585.html)

#### java的并发容器包Concurrent。阻塞队列，CopyOnWriteList等
* Concurrent：Java 5 添加了一个新的包到 Java 平台，java.util.concurrent 包。这个包包含有一系列能够让 Java 的并发编程变得更加简单轻松的类。在这个包被添加以前，你需要自己去动手实现自己的相关工具类。
* BlockingQueue：队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列；当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒。所谓的生产者-消费者模式就是借助这个东西进行数据交互的。
* CopyOnWriteList：读写分离，CopyOnWrite容器即写时复制的容器。通俗的理解是当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行Copy，复制出一个新的容器，然后新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。缺点：内存占用问题；数据一致性问题（只能保证最终一致，不能保证实时一致）。


####  ConcurrentHashMap的源理
![](http://p.blog.csdn.net/images/p_blog_csdn_net/liuzhengkang/EntryImages/20080912/58adc9e7b4725349c149a.jpg)
* 左边便是Hashtable的实现方式---锁整个hash表；而右边则是ConcurrentHashMap的实现方式---锁桶（或段）。ConcurrentHashMap将hash表分为16个桶（默认值），诸如get,put,remove等常用操作只锁当前需要用到的桶。试想，原来只能一个线程进入，现在却能同时16个写线程进入（写线程才需要锁定，而读线程几乎不受限制，之后会提到），并发性的提升是显而易见的。
* 更令人惊讶的是ConcurrentHashMap的读取并发，因为在读取的大多数时候都没有用到锁定，所以读取操作几乎是完全的并发操作，而写操作锁定的粒度又非常细，比起之前又更加快速（这一点在桶更多时表现得更明显些）。只有在求size等操作时才需要锁定整个表。而在迭代时，ConcurrentHashMap使用了不同于传统集合的快速失败迭代器（见之前的文章《JAVA API备忘---集合》）的另一种迭代方式，我们称为弱一致迭代器。在这种迭代方式中，当iterator被创建后集合再发生改变就不再是抛出ConcurrentModificationException，取而代之的是在改变时new新的数据从而不影响原有的数据，iterator完成后再将头指针替换为新的数据，这样iterator线程可以使用原来老的数据，而写线程也可以并发的完成改变，更重要的，这保证了多个线程并发执行的连续性和扩展性，是性能提升的关键。
* 何为弱一致迭代器我知道了。关键怎么实现？网上没有找到好的资料，**求解答**！
* see:[http://blog.csdn.net/liuzhengkang/article/details/2916620](http://blog.csdn.net/liuzhengkang/article/details/2916620)

#### java的线程池原理和自带的四大线程池
* Java通过Executors提供四种线程池，分别为：
	* newCachedThreadPool创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。
	* newFixedThreadPool 创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待。
	* newScheduledThreadPool 创建一个定长线程池，支持定时及周期性任务执行。
	* newSingleThreadExecutor 创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

#### Executor框架

#### 线程间通信【重点】
* 当线程调用了wait()方法时，它会释放掉对象的锁。另一个会导致线程暂停的方法：Thread.sleep()，它会导致线程睡眠指定的毫秒数，但线程在睡眠的过程中是不会释放掉对象的锁的。
* 多线程间通信方式：
	1. 共享变量
	2. wait/notify机制
	3. Lock/Condition机制
	4. 管道
* 进程与进程间通信
	1. 管道（Pipe）：管道可用于具有亲缘关系进程间的通信，允许一个进程和另一个与它有共同祖先的进程之间进行通信。
	2. 命名管道（named pipe）：命名管道克服了管道没有名字的限制，因此，除具有管道所具有的功能外，它还允许无亲缘关 系 进程间的通信。命名管道在文件系统中有对应的文件名。命名管道通过命令mkfifo或系统调用mkfifo来创建。
	3. 信号（Signal）：信号是比较复杂的通信方式，用于通知接受进程有某种事件发生，除了用于进程间通信外，进程还可以发送 信号给进程本身；Linux除了支持Unix早期信号语义函数sigal外，还支持语义符合Posix.1标准的信号函数sigaction（实际上，该函数是基于BSD的，BSD为了实现可靠信号机制，又能够统一对外接口，用sigaction函数重新实现了signal函数）。
	4. 消息（Message）队列：消息队列是消息的链接表，包括Posix消息队列system V消息队列。有足够权限的进程可以向队列中添加消息，被赋予读权限的进程则可以读走队列中的消息。消息队列克服了信号承载信息量少，管道只能承载无格式字节流以及缓冲区大小受限等缺
	5. 共享内存：使得多个进程可以访问同一块内存空间，是最快的可用IPC形式。是针对其他通信机制运行效率较低而设计的。往往与其它通信机制，如信号量结合使用，来达到进程间的同步及互斥。
	6. 内存映射（mapped memory）：内存映射允许任何多个进程间通信，每一个使用该机制的进程通过把一个共享的文件映射到自己的进程地址空间来实现它。
	7. 信号量（semaphore）：主要作为进程间以及同一进程不同线程之间的同步手段。
	8. 套接口（Socket）：更为一般的进程间通信机制，可用于不同机器之间的进程间通信。起初是由Unix系统的BSD分支开发出来的，但现在一般可以移植到其它类Unix系统上：Linux和System V的变种都支持套接字。

## IO
#### 继承关系图
![](http://img.blog.csdn.net/20160313181758645)
* 待补充


## Java虚拟机

#### 强烈建议阅读周志明的《深入理解Java虚拟机》
#### 强软弱虚四种引用
*  强引用：我们平时使用的没有经过特殊处理的实例都是强引用，特点：只要被引用，就不会被gc回收，使用不当就会导致内存溢出或泄露。
*  软引用：只有在内存不足时，gc才会回收这一部分引用。用于有用但不是必须的对象。软引用可用来实现内存敏感的高速缓存。用途：可以对一些消耗内存的对象比如：图片资源、handler进行包装。
*  弱引用：按照深入Java虚拟机作者的说法，在gc进行下一轮回收时，就会回收该部分引用，不管用没有用，不管内存是否够用。用于非必须的对象。用途：TODO:**求完善**
*  虚引用：按照深入Java虚拟机作者的说法，无法通过虚引用获取一个对象的实例。唯一的目的是在这个对象被回收的时候收到一个系统通知。用途：**求完善**

#### java内存分区
1. 程序计数器,简称pc,线程独享。
2. 虚拟机栈，线程独享。
3. 本地方法栈，线程独享。
4. 方法区（包括运行时常量池），线程共享。
5. 堆，线程共享。
6. 直接内存，这一块是直接操作操作系统的内存空间，比如NIO，直接就是操作的这块局域，减少虚拟机和操作系统的交互，目的是为了提高效率。

#### java的内存分区在什么情况下内存溢出
主要分为三块溢出，
第一块：虚拟机栈和本地方法栈，当栈调用层次过多和和扩展时无法申请到足够的空间，会出现StackOvewflow的异常和OutOfMemoryError:`java.lang.StackOverflowError`、`java.lang.OutOfMemoryError`
第二块：方法区的溢出，该区域也称为永久区。在jdk1.7中去除了永久区这一概念，因此不会再出现在该区域的溢出情况。`PermGen space`
第三块：堆溢出：`Java heap space`，这是重要的回收区域。
* 这一块局域分为:新生代（Eden+fromSurvivor+toSurvivor,8:1:1）和老年代,新生代大小：老年代大小：1:2。
* 新生代发生MinorGC,老年代发生FullGC/MajorGC。
* 当新生的对象（判定非大对象（需要连续内存空间））会直接放到新生代中，该区域会频繁发生minorGc，回收不再使用的对象。发生minorGc的方式：将新生代区域内的Eden以及fromSurvivor的有效对象移动到toSurvivor中，全部清空新生代+fromSurvivor。toSurvivor不够用，会使用一个类似银行中借款担保人，有一个担保区（老年代），把数据存放到担保区。
* 当新生的对象为大对象时，会直接放到老年区。还有一种情况，在新生代中每次发生gc的时候，对于没有回收的对象会进行计数加一，当超过一定阀值15，也会移动到老年区。在老年区会发生fullGc。


#### java垃圾回收机制，垃圾收集算法特点及工作在哪一代，分代收集策略，如何判断一个对象该被回收了，java对象实现如何自救，java的垃圾收集器，内存分配与回收及分配担保
* 判断对象是否该回收的算法：
	* 引用计数法：主流虚拟机不会使用，难以解决对象相互引用问题。
	* 可达性分析算法：主流虚拟机都在使用。
* GC回收算法：
	* 标记-清除算法：一般不使用，效率不高；会产生大量内存碎片。
	* 复制算法：新生代就是采用这种算法
	* 标记-整理算法（也称标记-压缩算法）：老年代采用这种算法
	* 分代收集算法：新生代+老年代。

	* 空间担保：在新生代发生MinorGC之前会检查老年代最大的可用连续空间是否大于新生代所有存活对象--->检查是否允许担保失败--->不允许---->FullGC----->允许--->根据历史记录评估是否够用----不够用，FullGC---->够用，担保失败，FullGC。
	* **求推荐**一篇讲这块讲的好的文章。

#### java的类加载机制，类加载的5个步骤，类加载器
* Java类的加载采用双亲委派模型，具体阐述：最底层是ApplicationClassLoader,负责加载jre中的核心jar包；下层是ExtClassLoader,主要负责加载一些支持包；下层是用户层的ClassLoader以及用户自定义的一些ClassLoader,负责加载用户层的Class文件。当新加载一个Class文件时，会从用户层一直向上层寻找，如果最上层不存在，依次去下层寻找，如果在最下层找不到该Class文件，就会抛出NoClassFindException的异常。这样设计的好处是：如果用户定义一个和系统一致的类（比如 java.lang.Object），保证只加载系统的类，而不是去加载用户的类，不然就全乱了。。
* 延伸一点：后期设计有违背双亲委派模型的，叫什么GSgi的组织，但是人家很好的解决了这一问题。
* 五个步骤：
	* 加载：获取二进制流、将静态存储结构转化为方法区的运行时数据结构、生成类对象。
	* 验证：验证文件格式是否符合规范、进行语音分析，看是否满足要求、字节码验证、符号引用验证。
	* 准备：正式分配内存设置初始值等。
	* 解析：将虚拟机常量池的符号引用转化为直接应用的过程、类解析、字段解析等
	* 初始化：真正执行Java程序代码。

#### JVM结构、GC工作机制详解
@see:[http://blog.csdn.net/tonytfjing/article/details/44278233](http://blog.csdn.net/tonytfjing/article/details/44278233)
  
