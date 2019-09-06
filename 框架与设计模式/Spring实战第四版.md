## 第1章　Spring之旅
所有的理念都可以追溯到Spring最根本的使命上：简化Java开发。

Spring的非侵入编程模型意味着这个类在Spring应用和非Spring应用中都可以发挥同样的作用。


创建应用组件之间协作的行为通常称为装配


基于XML的配置还是基于Java的配置


Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。

Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。

应用上下文:

	AnnotationConfigApplicationContext：从一个或多个
	基于Java的配置类中加载Spring应用上下文。
	AnnotationConfigWebApplicationContext：从一个或
	多个基于Java的配置类中加载Spring Web应用上下文。
	ClassPathXmlApplicationContext：从类路径下的一个或
	多个XML配置文件中加载上下文定义，把应用上下文的定义文件
	作为类资源。
	FileSystemXmlapplicationcontext：从文件系统下的一
	个或多个XML配置文件中加载上下文定义。
	XmlWebApplicationContext：从Web应用下的一个或多个
	XML配置文件中加载上下文定义。


## 第2章　装配Bean

Spring具有非常大的灵活性，它提供了三种主要的装配机制：

	在XML中进行显式配置。
	在Java中进行显式配置。
	隐式的bean发现机制和自动装配。

以上几个方法，可以混合使用。

##### 1. 自动化装配bean
Spring从两个角度来实现自动化装配：

	组件扫描（component scanning）：Spring会自动发现应用上下文
	中所创建的bean。
	自动装配（autowiring）：Spring自动满足bean之间的依赖。

@Component注解：这个简单的注解表明该类会作为组件类，并告知Spring要为这个类创建bean

@ComponentScan注解：启用了组件扫描


Spring应用上下文中所有的bean都会给定一个ID。在前面的例子中，
尽管我们没有明确地为SgtPeppersbean设置ID，但Spring会根据类
名为其指定一个ID。具体来讲，这个bean所给定的ID
为sgtPeppers，也就是将类名的第一个字母变为小写。

@Configuration注解表明这个类是一个配置类，该类应该包含在Spring应用上下文中如何创建bean的细节。

@Bean注解会告诉Spring这个方法将会返回一个对象，该对象要注册为Spring应用上下文中的bean。

Spring定义了多种作用域，可以基于这些作用域创建bean，包括：

	单例（Singleton）：在整个应用中，只创建bean的一个实例。
	原型（Prototype）：每次注入或者通过Spring应用上下文获取的
	时候，都会创建一个新的bean实例。
	会话（Session）：在Web应用中，为每个会话创建一个bean实
	例。
	请求（Rquest）：在Web应用中，为每个请求创建一个bean实
	例。

## 第4章　面向切面的Spring

![](https://upload-images.jianshu.io/upload_images/13390267-b4b54166635324d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

AOP已经形成了自己的术语。描述切面的常用术
语有通知（advice）、切点（pointcut）和连接点（join point）。

通知：切面也有目标——它必须要完成的工作。在AOP术语中，切面的工作被称为通知。

Spring切面可以应用5种类型的通知：

	前置通知（Before）：在目标方法被调用之前调用通知功能；
	后置通知（After）：在目标方法完成之后调用通知，此时不会关
	心方法的输出是什么；
	返回通知（After-returning）：在目标方法成功执行之后调用通
	知；
	异常通知（After-throwing）：在目标方法抛出异常后调用通知；
	环绕通知（Around）：通知包裹了被通知的方法，在被通知的方
	法调用之前和调用之后执行自定义的行为。

连接点：连接点是在应用执行过程中能够插入切面的一个点。

切点：切点的定义会匹配通知所要织入的一个或多个连接点。

切面：切面是通知和切点的结合。通知和切点共同定义了切面的全部内容。

引入：引入允许我们向现有的类添加新方法或属性。

织入：织入是把切面应用到目标对象并创建新的代理对象的过程。

在目标对象的生命周期里有多个点可以进行织入：

	编译期：切面在目标类编译时被织入。这种方式需要特殊的编译
	器。AspectJ的织入编译器就是以这种方式织入切面的。
	类加载期：切面在目标类加载到JVM时被织入。这种方式需要特
	殊的类加载器（ClassLoader），它可以在目标类被引入应用
	之前增强该目标类的字节码。AspectJ 5的加载时织入（load-time
	weaving，LTW）就支持以这种方式织入切面。
	运行期：切面在应用运行的某个时刻被织入。一般情况下，在织
	入切面时，AOP容器会为目标对象动态地创建一个代理对象。
	Spring AOP就是以这种方式织入切面的。

Spring提供了4种类型的AOP支持：

	基于代理的经典Spring AOP；
	纯POJO切面；
	@AspectJ注解驱动的切面；
	注入式AspectJ切面（适用于Spring各版本）。


Spring只支持方法级别的连接点

Spring借助AspectJ的切点表达式语言来定义Spring切面：

![](https://upload-images.jianshu.io/upload_images/13390267-5b8a5f24b07e425b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意只有execution指示器是实际执行匹配的，而其他的指示器都是用来限制匹配的。

![](https://upload-images.jianshu.io/upload_images/13390267-33883825e19dd69e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用注解创建切面：

@AspectJ注解进行了标注。该注解表明Audience不仅仅是一个POJO，还是一个切面

Spring使用AspectJ注解来声明通知方法：

![](https://upload-images.jianshu.io/upload_images/13390267-44ddddd3a224c785.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

@Pointcut注解能够在一个@AspectJ切面内定义可重用的切点。


@Around注解：环绕通知
它接受ProceedingJoinPoint作为参数。。这个对象是必须要有的，因为你要在通知中通过它来调用被通知的方法。通知方法中可以做任何的事情，当要将控制权交给被通知的方法时，它需要调用ProceedingJoinPoint的proceed()方法。


需要传入参数时：

![](https://upload-images.jianshu.io/upload_images/13390267-07b7dd35bb8112e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

利用被称为引入的AOP概念，切面可以为Spring bean添加新方法：

@DeclareParents注解由三部分组成：

	value属性指定了哪种类型的bean要引入该接口。在本例中，也就是所有实现Performance的类型。（标记符后面的加号表示是Performance的所有子类型，而不是Performance本
	身。）
	defaultImpl属性指定了为引入功能提供实现的类。在这里，我们指定的是DefaultEncoreable提供实现。
	@DeclareParents注解所标注的静态属性指明了要引入了接口。在这里，我们所引入的是Encoreable接口。

Spring的AOP配置元素能够以非侵入性的方式声明切面：

![](https://upload-images.jianshu.io/upload_images/13390267-6ec470e76b5f2f56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-a203fdad318d4ae9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第5章　构建Spring Web应用程序

Spring MVC所经历的所有站点：

![](https://upload-images.jianshu.io/upload_images/13390267-754afa1b7c9ae9b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


@EnableWebMvc注解：启用Spring MVC
@Controller注解：声明控制器
@RequestMapping注解：它的value属性指定了这个方法所要处理的请求路径，method属性细化了它所处理的HTTP方法。
@RequestParam注解：？连接的参数
@PathVariable("spittleId")：写在路径中的参数


Java校验API所提供的校验注解：

![](https://upload-images.jianshu.io/upload_images/13390267-a21f5ef7aa283f40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-d7f39099c141d36b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 第6章　渲染Web视图
JSP

Thymeleaf

前后端分离：

## 第7章　Spring MVC的高级技术
filter

7.5　跨重定向请求传递数据 redirect: 前缀


## 第8章　使用Spring Web Flow

Spring Web Flow是Spring MVC的扩展，它支持开发基于流程的应用程序。它将流程的定义与实现流程行为的类和视图分离开来。

流程是由三个主要元素定义的：状态、转移和流程数据

Spring Web Flow可供选择的状态：

![](https://upload-images.jianshu.io/upload_images/13390267-fdc2b24d6d81ff3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](https://upload-images.jianshu.io/upload_images/13390267-b85ea28e49a7f826.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


Spring Web Flow的作用域：

![](https://upload-images.jianshu.io/upload_images/13390267-c9491c6744e2e136.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


并不是所有的Web应用程序都是自由访问的。有时候，必须对用户进行指引、询问适当的问题并基于他们的响应将其引导到特定页面。在这些情况下，应用程序不太像一个菜单选项而更像应用程序与用户之
间的对话。


## 第9章　保护Web应用——Spring Security

Spring Security被分成了11个模块：

![](https://upload-images.jianshu.io/upload_images/13390267-044d895f41db50fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

过滤Web请求：DelegatingFilterProxy是一个特殊的Servlet Filter

@EnableWebSecurity注解将会启用Web安全功能。web应用下，使用@EnableWebMvcSecurity替代它。

预先增加验证用户有以下方法。
## 使用基于内存的用户存储

## 基于数据库表进行认证

## 基于LDAP进行认证

requiresChannel()方法会为选定的URL强制使用HTTPS。

我们可以使用requiresInsecure()代替requiresSecure()方法，将首页声明为始终通过HTTP传送。


##### 防止跨站请求伪造
Spring Security已经简化了将token放到请求的属性中这一任务。如果你使用Thymeleaf作为页面模板的话，只要<form>标签的action属性添加了Thymeleaf命名空间前缀，那么就会自动生成一个“_csrf”隐藏域。

通过调用csrf().disable()禁用Spring Security的CSRF防护功能。

##### 认证用户

重定向到首页登陆页。

## 第10章　通过Spring和JDBC征服数据库


## 第16章    RESTful架构
@RestController注解：为控制器默认设置消息转换


## 第18章　WebSocket
WebSocket协议提供了通过一个套接字实现全双工通信的功能。除了其他的功能之外，它能够实现Web浏览器和服务器之间的异步通信。全双工意味着服务器可以发送消息给浏览器，浏览器也可以发送消息给服务器。


## 第20章　使用JMX管理Spring Bean


## 第21章　Spring Boot


