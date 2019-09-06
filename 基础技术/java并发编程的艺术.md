## 第1章　并发编程的挑战
	vmstat		可以测量上下文切换的次数
	Lmbench3	可以测量上下文切换的时长。
	jstack		查看线程状态


## 第2章　Java并发机制的底层实现原理
### 1. volatile——下文讨论

### 2. synchronized
- 1.对于普通同步方法，锁是当前实例对象。
- 2.对于静态同步方法，锁是当前类的Class对象。
- 3.对于同步方法块，锁是Synchonized括号里配置的对象。

任何对象都有一个monitor与之关联，当且一个monitor被持有后，它将处于锁定状态。

synchronized关键字中，JVM基于进入和退出Monitor对象来实现锁。代码块同步是使用monitorenter和monitorexit指令实现的，而方法同步是使用另外一种方式实现的，细节在JVM规范里并没有详细说明。

synchronized用的锁是存在Java对象头里的。

### 3. 锁一共有3种：偏向锁、轻量级锁和重量级锁

偏向锁：大多数情况下，锁不仅不存在多线程竞争，由同一线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁。

![](https://upload-images.jianshu.io/upload_images/13390267-1e20334204b7a2f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

轻量级锁：加锁如果成功，当前线程获得锁，如果失败，表示其他线程竞争锁，当前线程便尝试使用自旋来获取锁。解锁如果失败，表示当前锁存在竞争，锁就会膨胀成重量级锁。

![](https://upload-images.jianshu.io/upload_images/13390267-f0ec546ac62e1727.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)	

一旦锁升级成重量级锁，就不会再恢复到轻量级锁状态。


### 4. CAS实现原子操作的三大问题
- 1. ABA问题
- 2. 循环时间长开销大
- 3. 只能保证一个共享变量的原子操作


## 第3章　Java内存模型
在并发编程中，需要处理两个关键问题：线程之间如何通信及线程之间如何同步。在命令式编程中，线程之间的通信机制有两种：1）共享内存 、2）消息传递。

在共享内存的并发模型里，线程之间共享程序的公共状态，通过写-读内存中的公共状态进行隐式通信。在消息传递的并发模型里，线程之间没有公共状态，线程之间必须通过发送消息来显式进行通信。

同步是指程序中用于控制不同线程间操作发生相对顺序的机制。在共享内存并发模型里，同步是显式进行的。程序员必须显式指定某个方法或某段代码需要在线程之间互斥执行。在消息传递的并发模型里，由于消息的发送必须在消息的接收之前，因此同步是隐式进行的。

Java的并发采用的是共享内存模型，Java线程之间的通信总是隐式进行，整个通信过程对程序员完全透明。编程时很可能会遇到各种奇怪的内存可见性问题。

##### 在Java中，所有实例域、静态域和数组元素都存储在堆内存中，堆内存在线程之间共享。

局部变量，方法定义参数和异常处理器参数不会在线程之间共享，它们不会有内存可见性问题，也不受内存模型的影响。


### JMM定义了线程和主内存之间的抽象关系
线程之间的共享变量存储在主内存中，每个线程都有一个私有的本地内存，本地内存中存储了该线程以读/写共享变量的副本。

本地内存是JMM的一个抽象概念，并不真实存在。它涵盖了缓存、写缓冲区、寄存器以及其他的硬件和编译器优化。

![](https://upload-images.jianshu.io/upload_images/13390267-d9f53f4bd4815413.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如果线程A与线程B之间要通信的话，必须要经历下面2个步骤。

	1. 线程A把本地内存A中更新过的共享变量刷新到主内存中去。
	2. 线程B到主内存中去读取线程A之前已更新过的共享变量。

![](https://upload-images.jianshu.io/upload_images/13390267-53d5e5cda59488df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了提高性能，编译器和处理器常常会对指令做重排序。重排序分3种类型。

![](https://upload-images.jianshu.io/upload_images/13390267-7576f7418f106f24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了保证内存可见性，Java编译器在生成指令序列的适当位置会插入内存屏障指令来禁止特定类型的处理器重排序。JMM把内存屏障指令分为4类。

![](https://upload-images.jianshu.io/upload_images/13390267-2eee96d9f3f6c724.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### happens-before规则：

![](https://upload-images.jianshu.io/upload_images/13390267-f237ad2b82e9b988.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 数据依赖性

如果两个操作访问同一个变量，且这两个操作中有一个为写操作，此时这两个操作之间就存在数据依赖性。

仅针对单个处理器中执行的指令序列和单个线程中执行的操作，编译器和处理器在重排序时，会遵守数据依赖性。

但是不同处理器之间和不同线程之间的数据依赖性不被编译器和处理器考虑。


as-if-serial语义使得单线程程序是按程序的顺序来执行的。as-if-serial语义使单线程程序员无需担心重排序会干扰他们，也无需担心内存可见性问题。

#### 顺序一致性——即程序的执行结果与该程序在顺序一致性内存模型中的执行结果相同。
顺序一致性内存模型的视图：

![](https://upload-images.jianshu.io/upload_images/13390267-a9cdd681c100c053.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


顺序一致性模型中，所有操作完全按程序的顺序串行执行。而在JMM中，临界区内的代码可以重排序。

JMM不保证未同步程序的执行结果与该程序在顺序一致性模型中的执行结果一致。

JMM不保证对64位的long型和double型变量的写操作具有原子性。

#### volatile的特性
即使是64位的long型和double型变量，只要它是volatile变量，对该变量的读/写就具有原子性。

如果是多个volatile操作或类似于volatile++这种复合操作，这些操作整体上不具有原子性。
	
volatile有：内存可见性、顺序一致性、但不具有原子性。

从内存语义的角度来说，volatile的写-读与锁的释放-获取有相同的内存效果：volatile写和锁的释放有相同的内存语义；volatile读与锁的获取有相同的内存语义。

当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内
存。

当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效。线程接下来将从主
内存中读取共享变量。（不缓存）

	总结：
	线程A写一个volatile变量，实质上是线程A向接下来将要读这个volatile变量的某个线程发出了（其对共享变量所做修改的）消息。

	线程B读一个volatile变量，实质上是线程B接收了之前某个线程发出的（在写这个volatile变量之前对共享变量所做修改的）消息。

	线程A写一个volatile变量，随后线程B读这个volatile变量，这个过程实质上是线程A通过主内存向线程B发送消息。


##### volatile重排序规则表：
![](https://upload-images.jianshu.io/upload_images/13390267-f6b3d492b062a442.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


JMM内存屏障插入策：

	在每个volatile写操作的前面插入一个StoreStore屏障。
	在每个volatile写操作的后面插入一个StoreLoad屏障。
	在每个volatile读操作的后面插入一个LoadLoad屏障。
	在每个volatile读操作的后面插入一个LoadStore屏障。

过多地使用volatile是不必要的，因为它会降低程序执行的效率。

	锁释放和锁获取的内存语义做个总结。
	
	线程A释放一个锁，实质上是线程A向接下来将要获取这个锁的某个线程发出了（线程A对共享变量所做修改的）消息。
	
	线程B获取一个锁，实质上是线程B接收了之前某个线程发出的（在释放这个锁之前对共享变量所做修改的）消息。

	线程A释放锁，随后线程B获取这个锁，这个过程实质上是线程A通过主内存向线程B发送消息。


### ReentrantLock的部分类图

![](https://upload-images.jianshu.io/upload_images/13390267-da803565631d9a53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)	

ReentrantLock分为公平锁和非公平锁：默认非公平锁。



#### concurrent包的实现
有一个通用化的实现模式：

	首先，声明共享变量为volatile。
	然后，使用CAS的原子条件更新来实现线程之间的同步。
	同时，配合以volatile的读/写和CAS所具有的volatile读和写的内存语义来实现线程之间的通信。

##### concurrent包的实现示意图

![](https://upload-images.jianshu.io/upload_images/13390267-4d19632a1f2d334d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### final域的内存语义——没有很深的理解

1）JMM禁止编译器把final域的写重排序到构造函数之外。
2）编译器会在final域的写之后，构造函数return之前，插入一个StoreStore屏障。这个屏障禁止处理器把final域的写重排序到构造函数之外。

JMM对这两种不同性质的重排序，采取了不同的策略：

	对于会改变程序执行结果的重排序，JMM要求编译器和处理器必须禁止这种重排序。
	对于不会改变程序执行结果的重排序，JMM对编译器和处理器不做要求（JMM允许这种重排序）。


### happens-before和as-if-serial语义

as-if-serial语义保证单线程内程序的执行结果不被改变，happens-before关系保证正确同
步的多线程程序的执行结果不被改变。

## 第4章　Java并发编程基础
### Java线程的状态

![](https://upload-images.jianshu.io/upload_images/13390267-d1699a2fa4de6c6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 线程创建之后，调用start()方法开始运行。执行Runnable的run()方法之后将会进入到终止状态。

### 线程间通信
![](https://upload-images.jianshu.io/upload_images/13390267-a3080f4816052959.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 等待/通知的相关方法
![](https://upload-images.jianshu.io/upload_images/13390267-b8dfd489ad5bf7bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 管道输入/输出流

管道输入/输出流和普通的文件输入/输出流或者网络输入/输出流不同之处在于，它主要用于线程之间的数据传输，而传输的媒介为内存。

线程A执行thread.join()语句：线程A等待thread线程终止之后才从thread.join()。

##第5章　Java中的锁
### Lock接口
锁获取与释放的可操作性、可中断的获取锁以及超时获取锁特性。

	Lock lock = new ReentrantLock();
	lock.lock();
	try {
	} finally {
	lock.unlock();
	}

##### Lock接口提供的synchronized关键字不具备的主要特性

![](https://upload-images.jianshu.io/upload_images/13390267-6313ca94f9015e9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 队列同步器AbstractQueuedSynchronizer（简称同步器）
锁是面向使用者的，它定义了使用者与锁交互的接口（比如可以允许两个线程并行访问），隐藏了实现细节。

同步器面向的是锁的实现者，它简化了锁的实现方式，屏蔽了同步状态管理、线程的排队、等待与唤醒等底层操作。

需要使用同步器提供的如下3个方法来访问或修改同步状态。

	getState()：获取当前同步状态。
	setState(int newState)：设置当前同步状态。
	compareAndSetState(int expect,int update)：使用CAS设置当前状态，该方法能够保证状态设置的原子性。

##### 同步器提供的模板方法
![](https://upload-images.jianshu.io/upload_images/13390267-c2493c1517158e0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 同步器提供的模板方法基本上分为3类：

	1. 独占式获取与释放同步状态
	2. 共享式获取与释放同步状态
	3. 查询同步队列中的等待线程情况

同步器依赖内部的同步队列（一个FIFO双向队列）来完成同步状态的管理

同步队列中的节点（Node）用来保存获取同步状态失败的线程引用、等待状态以及前驱和后继节点
![](https://upload-images.jianshu.io/upload_images/13390267-2ff663b576e43dcc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-64df8e26a6502ec5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-0d3bf50dab98ab26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


独占式同步状态获取流程，也就是acquire(int arg)方法调用流程
![](https://upload-images.jianshu.io/upload_images/13390267-c683be3e3086b25b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


共享式与独占式访问资源的对比
![](https://upload-images.jianshu.io/upload_images/13390267-b6ad307acbe3e562.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

独占式超时获取同步状态的流程
![](https://upload-images.jianshu.io/upload_images/13390267-66c6edc78f98e3ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)	



### ReentrantLock（可重入锁）
公平性：如果在绝对时间上，先对锁进行获取的请求一定先被满足，那么这个锁是公平的，反之，是不公平的。

##### 非公平性锁虽然可能造成线程“饥饿”，但极少的线程切换，保证了其更大的吞吐量。

### 读写锁ReentrantReadWriteLock

而读写锁在同一时刻可以允许多个读线程访问，但是在写线程访问时，所有的读线程和其他写线程均被阻塞。读写锁维护了一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性相比一般的排他锁有了很大提升。
![](https://upload-images.jianshu.io/upload_images/13390267-43dbb7978a5dc983.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### LockSupport工具类： 阻塞或唤醒一个线程
LockSupport提供的阻塞和唤醒方法
![](https://upload-images.jianshu.io/upload_images/13390267-8a5abba78b76f52d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Condition接口 （内部类）
Condition定义了等待/通知两种类型的方法，当前线程调用这些方法时，需要提前获取到Condition对象关联的锁。Condition对象是由Lock对象（调用Lock对象的newCondition()方法）创
建出来的，换句话说，Condition是依赖Lock对象的。

	Lock lock = new ReentrantLock();
	Condition condition = lock.newCondition();
	public void conditionWait() throws InterruptedException {
		lock.lock();
		try {
			condition.await();
		} finally {
			lock.unlock();
		}
	} public void conditionSignal() throws InterruptedException {
		lock.lock();
		try {
			condition.signal();
		} finally {
			lock.unlock();
		}
	}

![](https://upload-images.jianshu.io/upload_images/13390267-0d171d1b06d8232e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个Condition对象都包含着一个队列（以下称为等待队列），该队列是Condition对象实现等待/通知功能的关键。

![](https://upload-images.jianshu.io/upload_images/13390267-2c52962e5b109428.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同步队列与等待队列
![](https://upload-images.jianshu.io/upload_images/13390267-d3ac3989f4d19f5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Condition的实现是同步器的内部类，因此每个Condition实例都能够访问同步器提供的方法，相当于每个Condition都拥有所属同步器的引用。

调用Condition的await()方法（或者以await开头的方法），会使当前线程进入等待队列并释放锁，同时线程状态变为等待状态。

![](https://upload-images.jianshu.io/upload_images/13390267-e653cc22d09ba3fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

调用Condition的signal()方法，将会唤醒在等待队列中等待时间最长的节点（首节点），在唤醒节点之前，会将节点移到同步队列中。

![](https://upload-images.jianshu.io/upload_images/13390267-b533cb3099eae719.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Condition的signalAll()方法，相当于对等待队列中的每个节点均执行一次signal()方法，效
果就是将等待队列中所有节点全部移动到同步队列中，并唤醒每个节点的线程。


## 第6章　Java并发容器和框架

### ConcurrentHashMap的实现原理与使用

在并发编程中使用HashMap可能导致程序死循环。而使用线程安全的HashTable效率又非常低下，基于以上两个原因，便有了ConcurrentHashMap的登场机会。

在多线程环境下，使用HashMap进行put操作会引起死循环，导致CPU利用率接近100%。这是因为多线程会导致HashMap的Entry链表形成环形数据结构，一旦形成环形数据结构，Entry的next节点永远不为空，就会产生死循环获取Entry。


HashTable容器使用synchronized来保证线程安全，但在线程竞争激烈的情况下HashTable的效率非常低下。


##### ConcurrentHashMap的锁分段技术可有效提升并发访问率

ConcurrentHashMap的类图
![](https://upload-images.jianshu.io/upload_images/13390267-1175826e4b89ebb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ConcurrentHashMap的结构图
![](https://upload-images.jianshu.io/upload_images/13390267-7fa36b3de0eef316.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


segments数组的长度是2的N次方，假如concurrencyLevel等于14、15或16，ssize都会等于16，即容器里锁的个数也是16。

##### segments最大值是65535，在默认情况下segments等于16。


变量cap就是segment里HashEntry数组的长度，loadfactor默认等于0.75。

#### Segment的get操作、put操作和size操作。

get：先经过一次再散列，然后使用这个散列值通过散列运算定位到Segment，再通过散列算法定位到元素。

set：插入操作需要经历两个步骤，第一步判断是否需要对Segment里的HashEntry数组进行扩容，第二步定位添加元素的位置，然后将其放在HashEntry数组里。

值得一提的是，Segment的扩容判断比HashMap更恰当，因为HashMap是在插入元素后判断元素是否已经到达容量的，如果到达了就进行扩容，但是很有可能扩容之后没有新元素插入，这时HashMap就进行了一次无效的扩容。

在扩容的时候，首先会创建一个容量是原来容量两倍的数组，然后将原数组里的元素进行再散列后插入到新的数组里。为了高效，ConcurrentHashMap不会对整个容器进行扩容，而只对某个segment进行扩容。

### ConcurrentLinkedQueue的结构：之后用到在做详细了解。


### JDK 7提供了7个阻塞队列

	ArrayBlockingQueue：一个由数组结构组成的有界阻塞队列。
	LinkedBlockingQueue：一个由链表结构组成的有界阻塞队列。
	PriorityBlockingQueue：一个支持优先级排序的无界阻塞队列。
	DelayQueue：一个使用优先级队列实现的无界阻塞队列。
	SynchronousQueue：一个不存储元素的阻塞队列。
	LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。
	LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

### Fork/Join框架：类似归并排序，且为工作窃取模式，可以参看之前的笔记。


## 第7章　Java中的13个原子操作类

java.util.concurrent.atomic

使用方法基本一致，具体的使用到的时候，参看API。

## 第8章　Java中的并发工具类
### 等待多线程完成的CountDownLatch

CountDownLatch允许一个或多个线程等待其他线程完成操作。

	public class CountDownLatchTest {
		staticCountDownLatch c = new CountDownLatch(2);
		public static void main(String[] args) throws InterruptedException {
			new Thread(new Runnable() {
				@Override
				public void run() {
					System.out.println(1);
					c.countDown();
					System.out.println(2);
					c.countDown();
					}
				}).start();
				c.await();
				System.out.println("3");
			}
	}

### 同步屏障CyclicBarrier

它要做的事情是，让一组线程到达一个屏障（也可以叫同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行。

	public class CyclicBarrierTest {
		staticCyclicBarrier c = new CyclicBarrier(2);
		public static void main(String[] args) {
			new Thread(new Runnable() {
				@Override
				public void run() {
					try {
					c.await();
					} catch (Exception e) {
					}
					System.out.println(1);
					}
				}).start();
				try {
					c.await();
				} catch (Exception e) {
				}
			System.out.println(2);
		}
	}

CyclicBarrier可以用于多线程计算数据，最后合并计算结果的场景。

##### CountDownLatch的计数器只能使用一次，而CyclicBarrier的计数器可以使用reset()方法重置。所以CyclicBarrier能处理更为复杂的业务场景。例如，如果计算发生错误，可以重置计数器，并让线程重新执行一次。

### 控制并发线程数的Semaphore
Semaphore可以用于做流量控制

在一些列的线程中，只允许多少个可以并发运行。


### Exchanger 线程间交换数据
Exchanger：提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。这两个线程通过exchange方法交换数据，如果第一个线程先执行exchange()方法，它会一直等待第二个线程也执行exchange方法，当两个线程都到达同步点时，这两个线程就可以交换数据，将本线程生产出来的数据传递给对方。

## 第9章　Java中的线程池

### ThreadPoolExecutor执行execute()方法的示意图
![](https://upload-images.jianshu.io/upload_images/13390267-b57aa91949a8102c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### ThreadPoolExecutor执行示意图
![](https://upload-images.jianshu.io/upload_images/13390267-6d5038676f226ce6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	1）如果当前运行的线程少于corePoolSize，则创建新线程来执行任务（注意，执行这一步骤需要获取全局锁）。
	2）如果运行的线程等于或多于corePoolSize，则将任务加入BlockingQueue。
	3）如果无法将任务加入BlockingQueue（队列已满），则创建新的线程来处理任务（注意，执行这一步骤需要获取全局锁）。
	4）如果创建新线程将使当前运行的线程超出maximumPoolSize，任务将被拒绝，并调用RejectedExecutionHandler.rejectedExecution()方法。


ThreadPoolExecutor采取上述步骤的总体设计思路，是为了在执行execute()方法时，尽可能地避免获取全局锁（那将会是一个严重的可伸缩瓶颈）。在ThreadPoolExecutor完成预热之后（当前运行的线程数大于等于corePoolSize），几乎所有的execute()方法调用都是执行步骤2，而步骤2不需要获取全局锁。


#### 创建一个线程池时需要输入几个参数

##### 1 corePoolSize（线程池的基本大小）
##### 2 runnableTaskQueue（任务队列）阻塞队列
	ArrayBlockingQueue：
	LinkedBlockingQueue
	SynchronousQueue
	PriorityBlockingQueue

##### 3 maximumPoolSize（线程池最大数量）
##### 4 ThreadFactory：用于设置创建线程的工厂
##### 5 RejectedExecutionHandler（饱和策略）

	AbortPolicy：直接抛出异常。
	·CallerRunsPolicy：只用调用者所在线程来运行任务。
	·DiscardOldestPolicy：丢弃队列里最近的一个任务，并执行当前任务。
	·DiscardPolicy：不处理，丢弃掉。

##### 6 keepAliveTime 线程活动保持时间
##### 7 TimeUnit 线程活动保持时间的单位

### 向线程池提交任务

可以使用两个方法向线程池提交任务，分别为execute()和submit()方法。

1. execute()方法用于提交不需要返回值的任务
2. submit()方法用于提交需要返回值的任务。线程池会返回一个future类型的对象。


##### 通过调用线程池的shutdown或shutdownNow方法来关闭线程池。

#### 线程池的监控：可以使用以下属性
	taskCount：线程池需要执行的任务数量。
	completedTaskCount：线程池在运行过程中已完成的任务数量，小于或等于taskCount。
	largestPoolSize：线程池里曾经创建过的最大线程数量。通过这个数据可以知道线程池是否曾经满过。如该数值等于线程池的最大大小，则表示线程池曾经满过。
	getPoolSize：线程池的线程数量。如果线程池不销毁的话，线程池里的线程不会自动销毁，所以这个大小只增不减。
	getActiveCount：获取活动的线程数。

## 第10章　Executor框架
### Executor框架的两级调度模型

![](https://upload-images.jianshu.io/upload_images/13390267-4c2e32ba204cd33a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Executor框架主要由3大部分组成如下。

1. 任务：包括被执行任务需要实现的接口：Runnable接口或Callable接口。

2. 任务的执行：包括任务执行机制的核心接口Executor，以及继承自Executor的ExecutorService接口。Executor框架有两个关键类实现了ExecutorService接口（ThreadPoolExecutor和ScheduledThreadPoolExecutor）。

3. 异步计算的结果。包括接口Future和实现Future接口的FutureTask类

		Executor是一个接口，它是Executor框架的基础，它将任务的提交与任务的执行分离开
		来。

		ThreadPoolExecutor是线程池的核心实现类，用来执行被提交的任务。

		ScheduledThreadPoolExecutor是一个实现类，可以在给定的延迟后运行命令，或者定期执行命令。

		Future接口和实现Future接口的FutureTask类，代表异步计算的结果。

		Runnable接口和Callable接口的实现类，都可以被ThreadPoolExecutor或Scheduled-ThreadPoolExecutor执行。

![](https://upload-images.jianshu.io/upload_images/13390267-79c7f03b4590d9fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Executor框架的使用示意图

![](https://upload-images.jianshu.io/upload_images/13390267-7ceec5e5f02d1fd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Executor框架的成员
##### Executor框架的主要成员：ThreadPoolExecutor、ScheduledThreadPoolExecutor、Future接口、Runnable接口、Callable接口和Executors。

##### ThreadPoolExecutor：
Executors可以创建3种类型的ThreadPoolExecutor：SingleThreadExecutor、FixedThreadPool和CachedThreadPool。

##### ScheduledThreadPoolExecutor：
Executors可以创建2种类型的ScheduledThreadPoolExecutor：

1. ScheduledThreadPoolExecutor。包含若干个线程的ScheduledThreadPoolExecutor。
2. SingleThreadScheduledExecutor。只包含一个线程的ScheduledThreadPoolExecutor。

##### Future接口
Future接口和实现Future接口的FutureTask类用来表示异步计算的结果。当我们把Runnable接口或Callable接口的实现类提交（submit）给ThreadPoolExecutor或ScheduledThreadPoolExecutor时，ThreadPoolExecutor或ScheduledThreadPoolExecutor会向我们返回一个FutureTask对象

可以执行FutureTask.get()方法来等待任务执行完成。当任务成功完成后FutureTask.get()将返回该任务的结果。

##### Runnable接口和Callable接口

Runnable接口和Callable接口的实现类，都可以被ThreadPoolExecutor或Scheduled-ThreadPoolExecutor执行。它们之间的区别是Runnable不会返回结果，而Callable可以返回结果。

除了可以自己创建实现Callable接口的对象外，还可以使用工厂类Executors来把一个Runnable包装成一个Callable。

### ScheduledThreadPoolExecutor详解
ScheduledThreadPoolExecutor继承自ThreadPoolExecutor。它主要用来在给定的延迟之后运
行任务，或者定期执行任务。

##### FutureTask除了实现Future接口外，还实现了Runnable接口。因此，FutureTask可以交给Executor执行，也可以由调用线程直接执行（FutureTask.run()）。根据FutureTask.run()方法被执行的时机，FutureTask可以处于下面3种状态。

1. 未启动。FutureTask.run()方法还没有被执行之前，FutureTask处于未启动状态。当创建一个FutureTask，且没有执行FutureTask.run()方法之前，这个FutureTask处于未启动状态。
2. 已启动。FutureTask.run()方法被执行的过程中，FutureTask处于已启动状态。
3. 已完成。FutureTask.run()方法执行完后正常结束，或被取消（FutureTask.cancel（…）），或执行FutureTask.run()方法时抛出异常而异常结束，FutureTask处于已完成状态。

##### FutureTask的get和cancel的执行示意图

![](https://upload-images.jianshu.io/upload_images/13390267-b835e3c5daefa323.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### FutureTask的设计示意图
![](https://upload-images.jianshu.io/upload_images/13390267-9fae5b96ff8579fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



























