## java8 新特性
核心内容

- Lambda：函数式接口编程，简化代码量，类型上下文自动推断。
- Stream Api： 

### 1. Lambda 表达式
#####概念
java8引入面向函数编程，其中面向函数编程将函数作为参数传递，由于java是基于对象的，不存在单独的函数。因此java8取巧，采用单内部函数的接口作为函数参数，实习了传递函数的功能。Lambda表达式即实现了以上功能。

#####语法
lambda表达式的语法：由参数列表、箭头符号->和函数体组成。如：
		
	// 单句函数体无需{}
    Runnable r1 = () -> System.out.println("无参数的lambda表达式");
    r1.run();
	
	// 单输入参数可以省略
    Consumer<String> c1 = (name) -> System.out.println("我的大名是"+name);
    Consumer<String> c2 = name -> System.out.println("我的大名是"+name);
    c1.accept("zhangjue");
    c2.accept("张珏");
	
	// 单语句return可以省略
    Supplier<String> s1 = () -> "张珏教你学lambda";
    System.out.println(s1.get());
	
	// 范性中不支持基本类型
    //Function< String, int> f1 = name -> return (Integer)18;
    Function< String, Integer> f1 = name -> {
        return 18;
    };
    System.out.println("张珏今年"+f1.apply("zhangjue")+"岁");
	
	// {}最后记得加;
    Predicate<String> p1 = name -> {
        if( "张珏".equals(name))
        {
            return true;
        }
        else
        {
            return false;
        }
    };
	
    if(p1.test("zy")){
        System.out.println("找到了大神");
    }
    else {
        System.out.println("可能是菜鸡");
    }

##### 函数式接口
- 只包含一个抽象方法的接口，称为函数式接口
- @FunctionalInterface: 检验是否符号函数式接口要求
- java.util.function包：它里面包含了常用的函数式接口

给定的lambda表达式，它的类型是由其上下文推导而来。这意味着同样的lambda表达式在不同上下文里可以拥有不同的类型。这也是为什么lambda表达式中的参数不用写类型的原因。

例如：

	Callable c =()->"done";			//Callable类型
	PrivilegedAction a =()->"done";	//PrivilegedAction类型

目标类型 T 需要满足的条件：

- lambda表达式的参数和T的方法参数在数量和类型上一一对应
- lambda表达式的返回值和T的方法返回值相兼容
- lambda表达式内所抛出的异常和T的方法throws类型相兼容


 ![](https://upload-images.jianshu.io/upload_images/13390267-e63d38e1963309ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-a15c3d3700b1e9ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
	

### 3. 方法引用与构造器引用
使用现成的方法或者构造器代替lambda表达式。具体方式有以下几种。

#####方法引用
1. 实例对象::实例方法名
2. 类名::静态方法名
3. 类名::实例方法名

#####构造器引用
1. 类名::new
2. 类型[]::new

代码：

    String str = "zhangjue";
    Float f = 3.14f;
	
	//  实例对象::实例方法名，没有参数  
    //Supplier<String> sup = () -> str.toUpperCase();
    Supplier<String> sup = str::toUpperCase;
    System.out.println(sup.get());

	//  实例对象::实例方法名，有参数。
    //Consumer con = (x) -> System.out.println(x);
    Consumer<String> con = System.out::println; // 不指定参数
    con.accept(str);			//自动指定参数


	//类名::静态方法名。参数和上面的一样。
    //Function<Float, Integer> fun = (x) -> Math.round(x);
    Function<Float, Integer> fun = Math::round;
    System.out.println(fun.apply(f));

	//实例对象::方法名。参数和上面的一样。必须非静态方法。
    //Consumer<Client> cli = (x) -> x.say();
    Consumer<Client> cli = Client::sayHello;
    cli.accept(new Client());

	// 类名::new  
    //Function<String, StringBuffer> funConstruct = (x) -> new StringBuffer(x);
    Function<String, StringBuffer> funConstruct = StringBuffer::new;

	// 类型[]::new
    //Function<Integer, String[]> funString = (e) -> new String[e];
    Function<Integer, String[]> funString = String[]::new;
    String[] strArray = funString.apply(7);
        


### 4. Stream API
位于java.util.stream包：

Stream 是 Java8 提供一种处理数据的方式，多个中间操作可以连接起来形成一个流水操作，直到遇到终止操作时才一次性全部执行，称为“惰性求值”。

Stream API的并发采用的是我们熟悉的fork/join模式，采用Stream API来进行集合对象上的并发操作不需要编写多线程代码，也极大的简化了编程难度。Stream API 可以声明性地通过 parallel() 与 sequential() 在并行流与顺序流之间进行切换。

##### 特点
1. Stream 自己不会存储元素。
2. Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。 
3. Stream 操作是延迟执行的。这意味着他们会等到需要结果的时候才执行

使用一个Stream的步骤分为：创建、中间操作、终止操作。

![](https://upload-images.jianshu.io/upload_images/13390267-beba68e5be9d0bdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####创建 Stream 
1. Collection 接口被扩展，提供了两个获取流的方法：
	- default Stream<E> stream() : 返回一个顺序流
	- default Stream<E> parallelStream() : 返回一个并行流
	
			ArrayList<User> users = new ArrayList<>();
			Stream<User> stream = users.stream();
			Stream<User> stream = users. parallelStream();

2. Arrays 的静态方法 stream() 可 以获取数组流
	
	#####重载类型
	- public static IntStream stream(int[] array)
	- public static LongStream stream(long[] array)
	- public static DoubleStream stream(double[] array)

3. Stream.of(), 通过显示值 创建一个流。它可以接收任意数量的参数。

		String[] str = {"Hello World", "Hello Java", "Hello Steam"};
		Stream<String> stream = Stream.of(str);
		Stream<Integer> stream = Stream.of(1, 2, 3, 4);


4. Stream.iterate() 和 Stream.generate(), 创建无限流。


##### 中间操作

筛选与切片：

![](https://upload-images.jianshu.io/upload_images/13390267-c3b1152da96137d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

映射：

![](https://upload-images.jianshu.io/upload_images/13390267-50cfee7686f6029e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

排序：

![](https://upload-images.jianshu.io/upload_images/13390267-218a61ba85a5c4ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 终止操作(终端操作)
查找与匹配：

![](https://upload-images.jianshu.io/upload_images/13390267-ce5a68f433c51cac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-0126e91ad71b4b3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

归约：

![](https://upload-images.jianshu.io/upload_images/13390267-dc5ea73d88d3743b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####map 和 reduce 的连接通常称为 map-reduce 模式，被google所采用。

收集：

![](https://upload-images.jianshu.io/upload_images/13390267-b6d36b3d10e2e4f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


收集操作的静态方法

![](https://upload-images.jianshu.io/upload_images/13390267-07efa836fa25fee7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-2c385aae5a81e7a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：一个流只能执行一个结束操作，当执行了结束操作以后这个流就不能再被执行，也就是说不能再次进行中间操作或结束操作，所以结束操作一定是流的最后一个操作。


### 5. 接口中的默认方法与静态方法
Java 8中允许接口中包含具有具体实现的方法，该方法称为 “默认方法”，默认方法使用 default 关键字修饰。

##### 接口中允许添加静态方法。


##### 默认方法的”类优先”原则：

- 接口中定义了一个默认方法，父类定义了一个同名的方法时选择父类中的方法。

- 接口冲突：父接口提供一个默认方法，另一个接口也提供了一个具有相同名称和参数列表的方法(不管方法是否是默认方法)，那么必须覆盖该方法来解决冲突。


### 6. Optional 类
java.util.Optional包内。是一个容器类，代表一个值存在或不存在，原来用 null 表示一个值不存在，现在 Optional 可以避免空指针异常。
 
##### 常用方法

	Optional.of(T t)  	//创建一个 Optional 实例
	Optional.empty()  	//创建一个空的 Optional 实例
	Optional.ofNullable(T t) 	//若 t 不为 null,创建 Optional 实例,否则创建空实例 isPresent() 	//判断是否包含值
	orElse(T t) 	//如果调用对象包含值，返回该值，否则返回t
	orElseGet(Supplier s) 	//如果调用对象包含值，返回该值，否则返回 s 获取的值 
	map(Function f)	 //如果有值对其处理，并返回处理后的Optional，否则返回 Optional.empty() 
	flatMap(Function mapper) 	//与 map 类似，要求返回值必须是Optional
 
 
### 7. Fork/Join 框架

将一个大任务，进行拆分(fork)成若干个 小任务(拆到不可再拆时)，再将一个个的小任务运算的结果进行 join 汇总

##### 采用 “工作窃取”模式：

在一般的线程池中,如果一个线程正在执行的任务由于某些原因 无法继续运行,那么该线程会处于等待状态.而在fork/join框架实现中,如果 某个子问题由于等待另外一个子问题的完成而无法继续运行.那么处理该子 问题的线程会主动寻找其他尚未运行的子问题来执行.这种方式减少了线程 的等待时间,提高了性能.

![](https://upload-images.jianshu.io/upload_images/13390267-ee5bf2c5a8c9190e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

