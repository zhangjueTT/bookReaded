Spring 是一个容器, 因为它包含并且管理应用对象的生命周期

Spring 是框架，实现了使用简单的组件配置组合成一个复杂的应用. 在 Spring 中可以使用 XML 和 Java 注解组合这些对象

[Spring Framework Documentation](https://docs.spring.io/spring/docs/current/spring-framework-reference/index.html)


##### Spring 框架图：

![](https://upload-images.jianshu.io/upload_images/13390267-d4967986904f0aa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## The IoC container
**个人理解：**java思想中有一个很重要的是减少类间耦合，该方法的通俗做法就是轻继承，重依赖。而正是由于这个原因，java中需要通过构造器或者setter方法将所依赖的类（bean），注入到相应的类（bean）中。为了减轻繁杂的注入工作，避免注入错误，IoC 应运而生。

##### 几个关键类或者接口 --- 容器

	BeanFactory			//IoC的基础
	ApplicationContext	// BeanFactory的子接口，包装了BeanFactory。无特殊情况使用这个容器
	WebApplicationContext // 用于web的子接口
	
	// 具体实现类
	ClassPathXmlApplicationContext	// 大多数情况通过它载入xml配置文件
	FileSystemXmlApplicationContext

![](https://upload-images.jianshu.io/upload_images/13390267-7c5f50b095b886b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 官网两个xml一起使用例子
	ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

	// services.xml 文件
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <!-- services -->
	
	    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
	        <property name="accountDao" ref="accountDao"/>
	        <property name="itemDao" ref="itemDao"/>
	        <!-- additional collaborators and configuration for this bean go here -->
	    </bean>
	
	    <!-- more bean definitions for services go here -->
	
	</beans>

	// daos.xml 文件
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://www.springframework.org/schema/beans
	        http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <bean id="accountDao"
	        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
	        <!-- additional collaborators and configuration for this bean go here -->
	    </bean>
	
	    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
	        <!-- additional collaborators and configuration for this bean go here -->
	    </bean>
	
	    <!-- more bean definitions for data access objects go here -->
	
	</beans>


#### bean下面可以配置的属性：
##### class 指定类的全类名
##### id 为类取名字，需要唯一，并且可以用，分割多个名字
##### factory-method 指定为工厂方法创建，不指定是new创建
	<bean id="exampleBean" class="examples.ExampleBean"/>
	<bean id="clientService"
	    class="examples.ClientService"
	    factory-method="createInstance"/>
	    
	    
##### constructor-arg ：通过构造器注入，有以下几种方式区分注入。
	package x.y;

	public class Foo {
	
	    public Foo(Bar bar, Baz baz) {
	        // ...
	    }
	}
	
	// 根据顺序区分
	<beans>
	    <bean id="foo" class="x.y.Foo">
	        <constructor-arg ref="bar"/>
	        <constructor-arg ref="baz"/>
	    </bean>
	    <bean id="bar" class="x.y.Bar"/>	
	    <bean id="baz" class="x.y.Baz"/>
	</beans>
	// 根据类型区分
	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg type="int" value="7500000"/>
	    <constructor-arg type="java.lang.String" value="42"/>
	</bean>
	// 根据下标区分
	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg index="0" value="7500000"/>
	    <constructor-arg index="1" value="42"/>
	</bean>
	// 根据名字区分
	<bean id="exampleBean" class="examples.ExampleBean">
	    <constructor-arg name="years" value="7500000"/>
	    <constructor-arg name="ultimateAnswer" value="42"/>
	</bean>

##### properties：通过setter方法注入

	<bean id="exampleBean" class="examples.ExampleBean">
	    <property name="beanOne">
	        <ref bean="anotherExampleBean"/>
	    </property>
		 <property name="beanTwo" ref="yetAnotherBean"/>
	    <property name="integerProperty" value="1"/>
	</bean>
	
	<bean id="anotherExampleBean" class="examples.AnotherBean"/>
	<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>
	
	// 对应的类
	public class ExampleBean {
	
	    private AnotherBean beanOne;
	
	    private YetAnotherBean beanTwo;
	
	    private int i;
	
	    public void setBeanOne(AnotherBean beanOne) {
	        this.beanOne = beanOne;
	    }
	
	    public void setBeanTwo(YetAnotherBean beanTwo) {
	        this.beanTwo = beanTwo;
	    }
	
	    public void setIntegerProperty(int i) {
	        this.i = i;
	    }
	}
	
##### ref	将注入的值指向其他bean，id值或全类名
	<ref bean="someBean"/>

	//内部 beans 不能被外部指向。 
	<bean id="outer" class="...">
	    <!-- instead of using a reference to a target bean, simply define the target bean inline -->
	    <property name="target">
	        <bean class="com.example.Person"> <!-- this is the inner bean -->
	            <property name="name" value="Fiona Apple"/>
	            <property name="age" value="25"/>
	        </bean>
	    </property>
	</bean>

##### Collections 的引入
properties属性下的<list/>, <set/>, <map/>, and <props/> 标签可以注入Collection 容器对应的类型 List, Set, Map, and Properties.

	// map 映射
	public class Foo {
	    private Map<String, Float> accounts;
	    public void setAccounts(Map<String, Float> accounts) {
	        this.accounts = accounts;
	    }
	}
	// map 映射的一个例子
	<beans>
	    <bean id="foo" class="x.y.Foo">
	        <property name="accounts">
	            <map>
	                <entry key="one" value="9.99"/>
	                <entry key="two" value="2.75"/>
	                <entry key="six" value="3.99"/>
	            </map>
	        </property>
	    </bean>
	</beans>

##### p-namespace 属性操作
    <bean name="classic" class="com.example.ExampleBean">
        <property name="email" value="foo@bar.com"/>
    </bean>

    <bean name="p-namespace" class="com.example.ExampleBean"
        p:email="foo@bar.com"/>
        
##### c-namespace 构造器操作

##### depends-on bean的依赖
	<bean id="beanOne" class="ExampleBean" depends-on="manager"/>

##### 设置lazy-initialization mode
	<bean id="lazy" class="com.foo.ExpensiveToCreateBean" lazy-init="true"/>
	
	<beans default-lazy-init="true">
	    <!-- no beans will be pre-instantiated... -->
	</beans>

##### scope
singleton：只新建一个

	<bean id="accountService" class="com.foo.DefaultAccountService" scope="singleton"/>


prototype：每注入一次新建

	<bean id="accountService" class="com.foo.DefaultAccountService" scope="prototype"/>

request：每一个请求新建

session：每一个会话新建

application：每一个应用新建

WebSocket ：不详

##### alias 示例： 为bean取一个别名

	<alias name="fromName" alias="toName"/>
	// 两个字模块冲突时，使用alias就很有用了
	<alias name="subsystemA-dataSource" alias="subsystemB-dataSource"/>
	<alias name="subsystemA-dataSource" alias="myApp-dataSource" />
	//@Bean annotation也可以完成这个功能。


##### 获取bean： 调用 ApplicationContext 的 getBean() 

##### init-method、destroy-method

	<bean id="exampleInitBean" class="examples.ExampleBean" init-method="init"/>
	public class ExampleBean {
	
	    public void init() {
	        // do some initialization work
	    }
	}
	
	// 以上相当于以下
	<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
	public class AnotherExampleBean implements InitializingBean {
	
	    public void afterPropertiesSet() {
	        // do some initialization work
	    }
	}

	<bean id="exampleInitBean" class="examples.ExampleBean" destroy-method="cleanup"/>
	public class ExampleBean {
	
	    public void cleanup() {
	        // do some destruction work (like releasing pooled connections)
	    }
	}
	
	// 以上相当于以下
	<bean id="exampleInitBean" class="examples.AnotherExampleBean"/>
	public class AnotherExampleBean implements DisposableBean {
	
	    public void destroy() {
	        // do some destruction work (like releasing pooled connections)
	    }
	}

##### 还可以定制缺省的init和destroy方法。

初始化方法执行顺序

- Methods annotated with @PostConstruct

- afterPropertiesSet() as defined by the InitializingBean callback interface

- A custom configured init() method



Destroy methods 执行顺序

- Methods annotated with @PreDestroy

- destroy() as defined by the DisposableBean callback interface

- A custom configured destroy() method


##### Bean definition inheritance： parent属性
	<bean id="inheritedTestBean" abstract="true"
	        class="org.springframework.beans.TestBean">
	    <property name="name" value="parent"/>
	    <property name="age" value="1"/>
	</bean>
	
	<bean id="inheritsWithDifferentClass"
	        class="org.springframework.beans.DerivedTestBean"
	        parent="inheritedTestBean" init-method="initialize">
	    <property name="name" value="override"/>
	</bean>

##### abstract 将父类定义为抽象
	<bean id="inheritedTestBeanWithoutClass" abstract="true">
	    <property name="name" value="parent"/>
	    <property name="age" value="1"/>
	</bean>

##### BeanPostProcessor： 扩展容器的逻辑实现接口

### 基于注解的注入
##### Spring支持基于xml，基于注解，基于两者混合的工作方式。
	@Required annotation applies to bean property setter methods
	
	@Autowired annotation to constructors:
	@Autowired annotation to "traditional" setter methods:
	@Autowired annotation to method with arbitrary names and/or multiple arguments:
	@Autowired to fields as well and even mix it with constructors:
	@Autowired(required = false)

##### 配合@Autowired使用的属性
	@Primary
	@Qualifier：多个里面选一个

##### @PostConstruct and @PreDestroy
	public class CachingMovieLister {
	
	    @PostConstruct
	    public void populateMovieCache() {
	        // populates the movie cache upon initialization...
	    }
	
	    @PreDestroy
	    public void clearMovieCache() {
	        // clears the movie cache upon destruction...
	    }
	}

另外还有 @Component, @Service, and @Controller. @Ropositiry等注解

	@Configuration // 相当于beans
	public class AppConfig {
	
	    @Bean
	    public MyService myService() {
	        return new MyServiceImpl();
	    }
	}
	
	上面的等于：
	
	<beans>
	    <bean id="myService" class="com.acme.services.MyServiceImpl"/>
	</beans>

##### @Bean is a method-level annotation

##### Bean dependencies
	@Configuration
	public class AppConfig {
	
	    @Bean
	    public TransferService transferService(AccountRepository accountRepository) {
	        return new TransferServiceImpl(accountRepository);
	    }
	}

	// 定义逻辑顺序方法
	@Configuration
	public class AppConfig {
	
	    @Bean(initMethod = "init")
	    public Foo foo() {
	        return new Foo();
	    }
	
	    @Bean(destroyMethod = "cleanup")
	    public Bar bar() {
	        return new Bar();
	    }
	}

##### @Scope("prototype")

##### @Resource 注解要求提供一个 Bean 名称的属性，若该属性为空，则自动采用标注处的变量或方法名作为 Bean 的名称


## Spring Expression Language (SpEL)
类似 JsEL 表达是，通过#{}在注解中或者是XML文件中引入相应的计算或者是选择器功能。具体的内容等使用到的时候再继续学习。


## Spring AOP APIs
基于动态代理的模式实现的面向切面编程技术，该技术将逻辑功能代码与切面关注点代码分离，实现在一处写切面代码，而在所有需要的功能代码中注入的能力。

#### AOP 术语
1. 切面(Aspect): 定位多个逻辑功能代码的切面函数
2. 通知(Advice): 切面完成的工作
3. 目标(Target): 被通知的对象
4. 代理(Proxy): 向目标对象应用通知之后创建的对象
5. 连接点（Joinpoint）：逻辑功能代码执行的某个特定位置
6. 切点（pointcut）：使用到的连接点


##### @Aspect 注解，标注对应的bean是一个切面。
##### 切入点表达式execution：定义切面的大小。示例：	
	execution * com.atguigu.spring.ArithmeticCalculator.*(..): 匹配 ArithmeticCalculator 中声明的所有方法,第一个 * 代表任意修饰符及任意返回值. 第二个 * 代表任意方法. .. 匹配任意数量的参数. 若目标类与接口与该切面在同一个包中, 可以省略包名.
	execution public * ArithmeticCalculator.*(..): 匹配 ArithmeticCalculator 接口的所有公有方法.
	execution public double ArithmeticCalculator.*(..): 匹配 ArithmeticCalculator 中返回 double 类型数值的方法
	execution public double ArithmeticCalculator.*(double, ..): 匹配第一个参数为 double 类型的方法, .. 匹配任意数量任意类型的参数
	execution public double ArithmeticCalculator.*(double, double): 匹配参数类型为 double, double 类型的方法.
##### 切入点表达式可以通过操作符 &&, ||, ! 结合起来. 
	
##### 支持 5 种类型的通知注解
- @Before: 前置通知, 在方法执行之前执行
- @After: 后置通知, 在方法执行之后执行 
- @AfterRunning: 返回通知, 在方法返回结果之后执行
- @AfterThrowing: 异常通知, 在方法抛出异常之后
- @Around: 环绕通知, 围绕着方法执行

##### 指定切面的优先级 @Order ：值越小，优先级别越高

#### JoinPoint 在切面中获取功能代码的类

##### 通过 @Pointcut 注解：重复的切点提取成函数，然后被每个通知引入。

---
### spring AOP的方式有：xml，注解
基于注解的声明要优先于基于 XML 的声明. XML 声明切面时, 需要在 <beans> 根元素中导入 aop Schema。

所有的 Spring AOP 配置都必须定义在 <aop:config> 元素内部，对于每个切面而言, 都要创建一个 <aop:aspect>
![](https://upload-images.jianshu.io/upload_images/13390267-d1be1751cb11000a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 切入点使用 <aop:pointcut> 元素声明
- 定义在 <aop:aspect> 元素下: 只对当前切面有效
- 定义在 <aop:config> 元素下: 对所有切面都有效

通知元素需要使用 <pointcut-ref> 来引用切入点, 或用 <pointcut> 直接嵌入切入点表达式。

## 事务
Spring 既支持编程式事务管理, 也支持声明式的事务管理。

Spring 从不同的事务管理 API 中抽象了一整套的事务机制. 开发人员不必了解底层的事务 API, 就可以利用这些事务机制. 

事务管理作为一种横切关注点, 可以通过 AOP 方法模块化. Spring 通过 Spring AOP 框架支持声明式事务管理.

##### 事务管理是一种横切关注点
可以通过 tx Schema 中定义的 <tx:advice> 元素声明事务通知，为此必须事先将这个 Schema 定义添加到 <beans> 根元素中去.

@Transactional 注解来标注事务方法，只能标注公有方法，可以在方法或者类级别上添加。

事务传播属性：
![](https://upload-images.jianshu.io/upload_images/13390267-94ae6bba8f711559.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

@Transactional 的 isolation 属性中设置隔离级别.

@Transactional 注解的 rollbackFor 和 noRollbackFor 属性定义事物回滚.
