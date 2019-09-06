## 第二章    一切都是对象
	java栈中保存类的引用，堆中保存类。
	创建一个数组的时候，等于创建了一个引用数组。
	
	static：类变量，可以用实例变量访问，也可以直接用类名访问。

## 第三章    操作符
	无需考虑移植问题，所以没有sizeof()
	>>：有符号右移
	>>>：无符号右移

## 第四章    控制执行流程
	foreach：遍历数组和容器

	label + break | continue  实现 goto的功能。极少用

## 第五章    初始化与清理
	构造器的名字与类相同，故每个方法的手写字母小写的编码风格不适合构造函数。
	构造方法实际上是静态方法。

	this表示当前实例。只有需要区分时引用。
	
	this()：调用构造函数
	super()：调用父类构造函数
	必须位于构造函数的第一行，且两者不能共存。

	static（类）方法中没有this指针。
	static方法不能调用非static方法，反过来可以。

	finalize()：调用本地方法（C/C++）的时候，确保一些资源释放。很少用。

	初始化顺序：
	1. 父类构造 2. 成员初始化（按定义顺序） 3. 派生类构造。

	static代码段，会依据程序中使用到的顺序，依次初始化。

	销毁的顺序：与初始化相反。

#### 初始化顺序举例：
![](https://upload-images.jianshu.io/upload_images/13390267-191d7f991de0c677.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 可变参数列表：
	Object... args：其实就是在内部进行了一次的数组参数转换，不用显示的写数组。

## 第六章    访问控制权限
##### 字段&方法访问权限
	public ：都可以
	protect ：同一个包以及派生类
	default ：同一个包
	private ：仅内部

	java没有C的条件编译，采用import导包。
	java用包名替代命名空间的作用。

##### 类访问权限：
	public 
	default：包访问权限

	一个class文件中，只能有一个public类。

## 第七章    复用类
	has a
	is a

#### java的方法重载可以在基类与派生类之间进行，而C++不行。
	提供override，派生类重写基类方法。

	final字段：编译时常量，定义时赋值，一般需要加上static。
	空白final：定义时没有赋值，此时需要在构造器中赋值。
	final参数：类似C++的const
	final方法：不会被派生类覆盖，相对高效（差别不大）。private方法，隐式为final方法。
	final类：无法被继承。

## 第八章    多态
	向上转型：父类引用指向子类对象，子类扩展的方法不可用。
	向下转型：需要保证之前确实是子类对象给了父类引用。

	前期绑定（静态绑定）
	后期绑定（动态绑定）
	java中除了static方法和final（private）方法，都是动态绑定，C++只有virtual函数是动态绑定。

	基类构造函数调用动态方法。调用的是派生类的方法，但是派生类的变量此时还没有赋值，是默认初始值。

## 第九章    接口
	抽象类：含有至少一个抽象方法，abstract修饰。单继承
	接口：多继承。
	接口中的字段：都是static final的。
	接口访问：public  default
	接口中的方法：自动是public
	接口可以实现多继承
	接口可以继承接口
	接口多继承，如果存在函数冲突，会报错。

## 第十章    内部类
	可以访问外部类private域
	外部类名.this ：访问外部类的this
	外部类名.new 内部类：创建内部类。 
	
	匿名内部类：一次性使用。

## 第十一章    持有对象
	泛型（<>中指定的类型），使得编译器进行检查，而不会导致将错误类型放入到容器中。
	
	List：插入有序，不能随意
	Set：数据唯一
	Map：键值对
	Queue：队列

## 第十二章    通过异常处理错误
#### Java 异常结构
![](https://upload-images.jianshu.io/upload_images/13390267-190f9c33c89adb5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	非检查异常（unckecked exception）：Error 和 RuntimeException 以及他们的子类。
	检查异常（checked exception）：除了Error 和 RuntimeException的其它异常。
	
	throw：执行语句，直接抛出异常类
	throws：用在方法后，标识该方法可抛出的异常，可以接多个。

	try： 可能发生异常的语句
	catch： 可有多个，按顺序捕捉，一般从小到大覆盖所有可能。
	finally：资源释放等必须执行的语句。

	try块中即便有return，break，continue等改变执行流的语句，finally也会执行。

	Exception： 自定义异常一般需要继承自这个类。


## 第十三章    字符串

![](https://upload-images.jianshu.io/upload_images/13390267-491dfc526cb9d5d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	String	为字符串常量，而StringBuilder和StringBuffer均为字符串变量
	StringBuilder	是线程不安全的，而StringBuffer是线程安全的
	StringBuilder：	适用于单线程下在字符缓冲区进行大量操作的情况
	StringBuffer：	适用多线程下在字符缓冲区进行大量操作的情况

	编译器会用StringBuilder优化一些String操作，避免大量生成无用的中间字符串。

##### 格式化输出
	System.out.printf();
	System.out.format();
	String.format();

##### 正则表达式
	split()：将字符串从正则匹配的地方切开。

## 第十四章    类型信息
#### 加载类的一些准备：
![](https://upload-images.jianshu.io/upload_images/13390267-20be89f29780f1ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 限定RTTI的类型
	<？> 通配符
	<？extends Object>
	<? super Object>

	obj.instanceof(class)
	class.inInstance(obj)
	
		1.一个对象是本身类的一个对象
		2.一个对象是其基类和接口的一个对象
		3.所有对象都是Object
		4.凡是null有关的都是false  null.instanceof(class)


	

	类名.class和对象.getClass()几乎没有区别，因为一个类被类加载器加载后，就是唯一的一个类。

#### 反射
Class类与java.lang.reflect类库一起对反射进行了支持，该类库包含Field、Method和Constructor类，这些类的对象由JVM在启动时创建，用以表示未知类里对应的成员。这样的话就可以使用Contructor创建新的对象，用get()和set()方法获取和修改类中与Field对象关联的字段，用invoke()方法调用与Method对象关联的方法。另外，还可以调用getFields()、getMethods()和getConstructors()等许多便利的方法，以返回表示字段、方法、以及构造器对象的数组，这样，对象信息可以在运行时被完全确定下来，而在编译时不需要知道关于类的任何事情。


#### 动态代理
Java的动态代理可以动态地创建并代理并动态地处理对所代理方法的调用。在动态代理上所做的所有调用都会被重定向到单一的调用处理器上，它的工作是揭示调用的类型并确定相应的策略。
	
	// 原始类与接口
	public interface Interface {
	    void doSomething();
	    void somethingElse(String arg);
	}
	public class RealObject implements Interface {
	    public void doSomething() {
	        System.out.println("doSomething.");
	    }
	    public void somethingElse(String arg) {
	        System.out.println("somethingElse " + arg);
	    }
	}

	// 动态代理类
	public class DynamicProxyHandler implements InvocationHandler {
	    private Object proxyed;
	    
	    public DynamicProxyHandler(Object proxyed) {
	        this.proxyed = proxyed;
	    }
	    
	    @Override
	    public Object invoke(Object proxy, Method method, Object[] args) throws IllegalAccessException, IllegalArgumentException, InvocationTargetException {
	        System.out.println("代理工作了.");
	        return method.invoke(proxyed, args);
	    }
	}
	
	// client
	public class Main {
	    public static void main(String[] args) {
	        RealObject real = new RealObject();
	        Interface proxy = (Interface) Proxy.newProxyInstance(
	                Interface.class.getClassLoader(), new Class[] {Interface.class},
	                new DynamicProxyHandler(real));
	        
	        proxy.doSomething();
	        proxy.somethingElse("luoxn28");
	    }
	}

通过调用Proxy静态方法Proxy.newProxyInstance()可以创建动态代理，这个方法需要得到一个类加载器，一个你希望该代理实现的接口列表(不是类或抽象类)，以及InvocationHandler的一个实现类。

动态代理可以将所有调用重定向到调用处理器，因此通常会调用处理器的构造器传递一个”实际”对象的引用，从而将调用处理器在执行中介任务时，将请求转发。

## 第十五章    泛型
泛型类似C++中的模板，增加编译时安全检查，同时使得同一个算法应用于不同的数据类型，增加代码复用性。

泛型可用于：接口、方法、匿名内部类、类中。

在泛型方法内部，无法获得有关泛型参数的信息。java泛型擦除了类型信息，因此List<Integer> 和 List<String> 是一样的。

引入通配符限定泛型的范围。

	<？> 通配符
	<？extends Object>
	<? super Object>


1. 任何基本类型都不能作为泛型的参数。

泛型由于类型擦除，无法被catch捕捉到，也无法被继承Throwable。

## 第十七章    容器深入研究
![](https://upload-images.jianshu.io/upload_images/13390267-2b559f81f3625494.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 正确的equals要满足的条件
![](https://upload-images.jianshu.io/upload_images/13390267-aa65f075712faefc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 第十八章    java IO系统
#### 基于字节的IO操作

![](https://upload-images.jianshu.io/upload_images/13390267-f36f5c377df99886.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-f19f3e9e7f9a659a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 基于字符的IO操作

![](https://upload-images.jianshu.io/upload_images/13390267-2b43263c1ac9aa30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-1c22efcaf4ed516c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 序列化和反序列化的概念
	把对象转换为字节序列的过程称为对象的序列化。
	把字节序列恢复为对象的过程称为对象的反序列化。

	对象的序列化主要有两种用途：
	1） 把对象的字节序列永久地保存到硬盘上，通常存放在一个文件中；
	2） 在网络上传送对象的字节序列。

只有实现了Serializable或Externalizable接口的类的对象才能被序列化。Externalizable接口继承自 Serializable接口，实现Externalizable接口的类完全由自身来控制序列化的行为，而仅实现Serializable接口的类可以 采用默认的序列化方式 。

##### 对象序列化包括如下步骤：
	1） 创建一个对象输出流，它可以包装一个其他类型的目标输出流，如文件输出流；
	2） 通过对象输出流的writeObject()方法写对象。

##### 对象反序列化的步骤如下：
	1） 创建一个对象输入流，它可以包装一个其他类型的源输入流，如文件输入流；
	2） 通过对象输入流的readObject()方法读取对象。

##### serialVersionUID 
	凡是实现Serializable接口的类都有一个表示序列化版本标识符的静态变量
	private static final long serialVersionUID = -5809782578272943999L;


	serialVersionUID的取值是Java运行时环境根据类的内部细节自动生成，任何修改都可能变化。
	用不同的Java编译器编译，有可能会导致不同的 serialVersionUID，也有可能相同。

	为了提高serialVersionUID的独立性和确定性，强烈建议在一个可序列化类中显示的定义serialVersionUID，为它赋予明确的值。

##### 显式地定义serialVersionUID有两种用途：
	1. 在某些场合，希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有相同的serialVersionUID；
	2. 在某些场合，不希望类的不同版本对序列化兼容，因此需要确保类的不同版本具有不同的serialVersionUID。

## 第二十一章    并发
	实现Runnable接口中的run方法：不返回任何值。
	实现Callable接口中的call方法：可以返回值。必须从ExecutorService,submit()中使用它。
	继承自Thread

	sleep()：不释放锁。
	yield()：具有相同优先级的线程先运行。
	setDaemon()：后台运行。
	join()：等待加入线程完成后，执行。

#### 线程状态

![](https://upload-images.jianshu.io/upload_images/13390267-d1b39b7215a29491.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 死锁条件——同时满足

![](https://upload-images.jianshu.io/upload_images/13390267-3a8c7116f1013d3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 乐观锁和悲观锁
悲观锁：总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁。

传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。再比如Java里面的同步原语synchronized关键字的实现也是悲观锁。

乐观锁：每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号、CAS等机制。

乐观锁适用于多读的应用类型，这样可以提高吞吐量，像数据库提供的类似于write_condition机制，其实都是提供的乐观锁。在Java中java.util.concurrent.atomic包下面的原子变量类就是使用了乐观锁的一种实现方式CAS实现的。
	
version方式：一般是在数据表中加上一个数据版本号version字段，表示数据被修改的次数，当数据被修改时，version值会加一。当线程A要更新数据值时，在读取数据的同时也会读取version值，在提交更新时，若刚才读取到的version值为当前数据库中的version值相等时才更新，否则重试更新操作，直到更新成功。

CAS操作方式：即compare and swap 或者 compare and set，涉及到三个操作数，数据所在的内存值，预期值，新值。当需要更新时，判断当前内存值与之前取到的值是否相等，若相等，则用新值更新，若失败则重试，一般情况下是一个自旋操作，即不断的重试。




	