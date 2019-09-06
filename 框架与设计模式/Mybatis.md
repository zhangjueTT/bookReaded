mybatis的官方中文文档有较为详尽的资料，建议使用前先浏览一遍官方文档。[官方中文文档](http://www.mybatis.org/mybatis-3/zh/getting-started.html)

## 入门：
#### maven 引入包

	<dependency>
	  <groupId>org.mybatis</groupId>
	  <artifactId>mybatis</artifactId>
	  <version>x.x.x</version>
	</dependency>

#### 从 XML 中构建 SqlSessionFactory

	// 配置文件路径根据你自己的情况定义，SqlSessionFactory全局定义一个就好了。
	String resource = "org/mybatis/example/mybatis-config.xml";
	InputStream inputStream = Resources.getResourceAsStream(resource);
	SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	/*
		不使用 XML 构建 SqlSessionFactory
		具体方法查文档
	*/

#### 从 SqlSessionFactory 中获取 SqlSession

SqlSession 完全包含了面向数据库执行 SQL 命令所需的所有方法，可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。

	SqlSession session = sqlSessionFactory.openSession();
	try {
	  Blog blog = (Blog) session.selectOne("org.mybatis.example.BlogMapper.selectBlog", 101);
	} finally {
	  session.close();
	}

也可以通过mapper文件（或相对应的类）来执行SQL语句。

	SqlSession session = sqlSessionFactory.openSession();
	try {
	  BlogMapper mapper = session.getMapper(BlogMapper.class);
	  Blog blog = mapper.selectBlog(101);
	} finally {
	  session.close();
	}

对应的mapper文件

	<?xml version="1.0" encoding="UTF-8" ?>
	<!DOCTYPE mapper
	  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	<mapper namespace="org.mybatis.example.BlogMapper">
	  <select id="selectBlog" resultType="Blog">
	    select * from Blog where id = #{id}
	  </select>
	</mapper>
	
	// java 注解也可以处理简单的sql语句，替代mapper文件
	package org.mybatis.example;
	public interface BlogMapper {
	  @Select("SELECT * FROM blog WHERE id = #{id}")
	  Blog selectBlog(int id);
	}

##### 以下谈谈在插件中使用到的几个类：	
Executor与Sqlsession：Sqlsession只是个门面，真正干事的是Executor，Sqlsession对数据库的操作都是通过Executor来完成的。

Executor本质上也是个甩手掌柜，具体的事情原来是StatementHandler来完成的。

StatementHandler 的一个实现类是 PreparedStatementHandler。

结果处理使用ResultSetHandler来完成，默认的ResultSetHandler是FastResultSetHandler
	
	
#### 作用域（Scope）和生命周期的一些问题
##### SqlSessionFactoryBuilder 方法域
这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。你可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory 实例，但是最好还是不要让其一直存在以保证所有的 XML 解析资源开放给更重要的事情。
	
##### SqlSessionFactory	全局域
SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由对它进行清除或重建。使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，多次重建 SqlSessionFactory 被视为一种代码“坏味道（bad smell）”。因此 SqlSessionFactory 的最佳作用域是应用作用域。有很多方法可以做到，最简单的就是使用单例模式或者静态单例模式。

##### SqlSession	不是线程安全的，请求或方法作用域
每个线程都应该有它自己的 SqlSession 实例。SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。也绝不能将 SqlSession 实例的引用放在任何类型的管理作用域中，比如 Servlet 架构中的 HttpSession。如果你现在正在使用一种 Web 框架，要考虑 SqlSession 放在一个和 HTTP 请求对象相似的作用域中。换句话说，每次收到的 HTTP 请求，就可以打开一个 SqlSession，返回一个响应，就关闭它。这个关闭操作是很重要的，你应该把这个关闭操作放到 finally 块中以确保每次都能执行关闭。下面的示例就是一个确保 SqlSession 关闭的标准模式：

	SqlSession session = sqlSessionFactory.openSession();
	try {
	  // do work
	} finally {
	  session.close();
	}

#### getMapper
映射器是一个你创建来绑定你映射的语句的接口。映射器接口的实例是从 SqlSession 中获得的。


## mybatis 配置文件

##### configuration节点为根节点。在configuration节点之下，我们可以配置9个子节点，分别为：
	properties 	// 可外部配置且可动态替换的配置文件
	typeAliases	// 重命名过长的全类名
	objectFactory // 包装结果对象的新实例

	environments  // MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之一，一个SqlSessionFactory只能选择其一
	
	databaseIdProvider // 可以根据不同的数据库厂商执行不同的语句
	typeHandlers	// 将获取的值以合适的方式转换成 Java 类型
	mappers		// 定义 SQL 映射语句了，告诉 MyBatis 到哪里去找到这些语句，让mybatis用来建立数据表和javabean映射的一个桥梁
	
	/*
		一个全配置的示例，具体含义可查官方文档。
	  <settings>
		<setting name="cacheEnabled" value="true"/>
		<setting name="lazyLoadingEnabled" value="true"/>
		<setting name="multipleResultSetsEnabled" value="true"/>
		<setting name="useColumnLabel" value="true"/>
		<setting name="useGeneratedKeys" value="false"/>
		<setting name="autoMappingBehavior" value="PARTIAL"/>
		<setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
		<setting name="defaultExecutorType" value="SIMPLE"/>
		<setting name="defaultStatementTimeout" value="25"/>
		<setting name="defaultFetchSize" value="100"/>
		<setting name="safeRowBoundsEnabled" value="false"/>
		<setting name="mapUnderscoreToCamelCase" value="false"/>
		<setting name="localCacheScope" value="SESSION"/>
		<setting name="jdbcTypeForNull" value="OTHER"/>
		<setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
	  </settings>
	*/
	settings      // MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为

	
	/*
		MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：	
		Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
		ParameterHandler (getParameterObject, setParameters)
		ResultSetHandler (handleResultSets, handleOutputParameters)
		StatementHandler (prepare, parameterize, batch, update, query)
	*/
	plugins		// 允许你在已映射语句执行过程中的某一点进行拦截调用
	

## Mapper 映射文件
##### 以mapper作为根节点，其下面可以配置的元素节点有： 

	cache – 给定命名空间的缓存配置。
	cache-ref – 其他命名空间缓存配置的引用。
	resultMap – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。
	parameterMap – 已废弃！老式风格的参数映射。内联参数是首选,这个元素可能在将来被移除，这里不会记录。
	sql – 可被其他语句引用的可重用语句块。
	insert – 映射插入语句
	update – 映射更新语句
	delete – 映射删除语句
	select – 映射查询语句

##### select的一个示例，具体每属性的含义可查文档。
	<select
	  id="selectPerson"
	  parameterType="int"
	  parameterMap="deprecated"
	  resultType="hashmap"
	  resultMap="personResultMap"
	  flushCache="false"
	  useCache="true"
	  timeout="10000"
	  fetchSize="256"
	  statementType="PREPARED"
	  resultSetType="FORWARD_ONLY">
	  
	<select id="selectPerson" parameterType="int" resultType="hashmap">
	  SELECT * FROM PERSON WHERE ID = #{id}
	</select>

##### insert，update 和 delete 的一个示例：
	<insert
	  id="insertAuthor"
	  parameterType="domain.blog.Author"
	  flushCache="true"
	  statementType="PREPARED"
	  keyProperty=""
	  keyColumn=""
	  useGeneratedKeys=""
	  timeout="20">
	
	<update
	  id="updateAuthor"
	  parameterType="domain.blog.Author"
	  flushCache="true"
	  statementType="PREPARED"
	  timeout="20">
	
	<delete
	  id="deleteAuthor"
	  parameterType="domain.blog.Author"
	  flushCache="true"
	  statementType="PREPARED"
	  timeout="20">
	  
	<insert id="insertAuthor">
	  insert into Author (id,username,password,email,bio)
	  values (#{id},#{username},#{password},#{email},#{bio})
	</insert>
	
	<update id="updateAuthor">
	  update Author set
	    username = #{username},
	    password = #{password},
	    email = #{email},
	    bio = #{bio}
	  where id = #{id}
	</update>
	
	<delete id="deleteAuthor">
	  delete from Author where id = #{id}
	</delete>
	
##### sql这个元素可以被用来定义可重用的 SQL 代码段
	// 这里定义
	<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
	
	// 这里引用
	<select id="selectUsers" resultType="map">
	  select
	    <include refid="userColumns"><property name="alias" value="t1"/></include>,
	    <include refid="userColumns"><property name="alias" value="t2"/></include>
	  from some_table t1
	    cross join some_table t2
	</select>

##### Parameters

	<insert id="insertUser" parameterType="User">
	  insert into users (id, username, password)
	  values (#{id}, #{username}, #{password})
	</insert>
	
	<select id="selectUsers" resultType="User">
	  select id, username, password
	  from users
	  where id = #{id}
	</select>

允许插入到类里，神奇吧！！！

##### 插入一个不转义的字符串
	#{} 格式的语法会导致 MyBatis 创建 PreparedStatement 参数并安全地设置参数
	// 以下语句不转义
	ORDER BY ${columnName}
	
##### Result Maps
将结果映射到resultType指定的类中，这个功能很强大，你遇到相应的情况的时候再来查文档吧。

	<select id="selectUsers" resultType="User">
	  select
	    user_id             as "id",
	    user_name           as "userName",
	    hashed_password     as "hashedPassword"
	  from some_table
	  where id = #{id}
	</select>
	
##### 缓存
默认开启一级缓存，这里使用的缓存代表的是二级缓存，二级缓存需要手动开启。

要开启二级缓存,你需要在你的 SQL 映射文件中添加一行
	
	<cache/>
该语句默认做了以下工作：

	映射语句文件中的所有 select 语句将会被缓存。
	映射语句文件中的所有 insert,update 和 delete 语句会刷新缓存。
	缓存会使用 Least Recently Used(LRU,最近最少使用的)算法来收回。
	根据时间表(比如 no Flush Interval,没有刷新间隔), 缓存不会以任何时间顺序 来刷新。
	缓存会存储列表集合或对象(无论查询方法返回什么)的 1024 个引用。
	缓存会被视为是 read/write(可读/可写)的缓存,意味着对象检索不是共享的,而 且可以安全地被调用者修改,而不干扰其他调用者或线程所做的潜在修改。


##### 缓存回收算法：
	LRU – 最近最少使用的:移除最长时间不被使用的对象。
	FIFO – 先进先出:按对象进入缓存的顺序来移除它们。
	SOFT – 软引用:移除基于垃圾回收器状态和软引用规则的对象。
	WEAK – 弱引用:更积极地移除基于垃圾收集器状态和弱引用规则的对象。

示例：

	<cache
	  eviction="FIFO"
	  flushInterval="60000"
	  size="512"
	  readOnly="true"/>
  
这个更高级的配置创建了一个 FIFO 缓存,并每隔 60 秒刷新,存数结果对象或列表的 512 个引用,而且返回的对象被认为是只读的,因此在不同线程中的调用者之间修改它们会 导致冲突。

还可以使用自定义缓存，这个等你需要用到的时候查文档吧。

##### 参照缓存
引用其他的缓存内容

	<cache-ref namespace="com.someone.application.data.SomeMapper"/>


## 动态 SQL
摆脱拼接 SQL 语句的痛苦

### if
	<select id="findActiveBlogLike"
	     resultType="Blog">
	  SELECT * FROM BLOG WHERE state = ‘ACTIVE’ 
	  <if test="title != null">
	    AND title like #{title}
	  </if>
	  <if test="author != null and author.name != null">
	    AND author_name like #{author.name}
	  </if>
	</select>
	
### choose, when, otherwise
choose 元素，它有点像 Java 中的 switch 语句。

	<select id="findActiveBlogLike"
	     resultType="Blog">
	  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
	  <choose>
	    <when test="title != null">
	      AND title like #{title}
	    </when>
	    <when test="author != null and author.name != null">
	      AND author_name like #{author.name}
	    </when>
	    <otherwise>
	      AND featured = 1
	    </otherwise>
	  </choose>
	</select>

### trim, where, set
避免拼接sql时出现以下问题

	SELECT * FROM BLOG
	WHERE 
	AND title like ‘someTitle’	

trim使用

	<trim prefix="WHERE" prefixOverrides="AND |OR ">
	  ... 
	</trim>	
	
set的使用
	
	<update id="updateAuthorIfNecessary">
	  update Author
	    <set>
	      <if test="username != null">username=#{username},</if>
	      <if test="password != null">password=#{password},</if>
	      <if test="email != null">email=#{email},</if>
	      <if test="bio != null">bio=#{bio}</if>
	    </set>
	  where id=#{id}
	</update>

set的真面目
	
	//注意这里我们删去的是后缀值，同时添加了前缀值。
	<trim prefix="SET" suffixOverrides=",">
	  ...
	</trim>
	
### foreach 对一个集合进行遍历，通常是在构建 IN 条件语句的时候

	<select id="selectPostIn" resultType="domain.blog.Post">
	  SELECT *
	  FROM POST P
	  WHERE ID in
	  <foreach item="item" index="index" collection="list"
	      open="(" separator="," close=")">
	        #{item}
	  </foreach>
	</select>

## 关于插件
MyBatis提供了一种插件(plugin)的功能，其中实现拦截器功能。Mybatis采用责任链模式，通过动态代理组织多个拦截器，通过这些拦截器可以改变Mybatis的默认行为。

每一个拦截器都必须实现上面的三个方法：

	//注解里描述的是指定拦截方法的签名 [type,method,args] （即对哪种对象的哪种方法进行拦截），它在拦截前用于决断。
	@Intercepts({@Signature(type = Executor.class, method ="update", args = {MappedStatement.class, Object.class})})  
	public class ExamplePlugin implements Interceptor {  
	    @Override  
	    public Object intercept(Invocation invocation) throws Throwable {  
	        return invocation.proceed();  
	    }  
	  
	    @Override  
	    public Object plugin(Object target) {  
	        return Plugin.wrap(target, this);  
	    }  
	  
	    @Override  
	    public void setProperties(Properties properties) {  
	    }
	}

1. Object intercept(Invocation invocation)是实现拦截逻辑的地方，内部要通过invocation.proceed()显式地推进责任链前进，也就是调用下一个拦截器拦截目标方法。

2. Object plugin(Object target)就是用当前这个拦截器生成对目标target的代理，实际是通过Plugin.wrap(target,this)来完成的，把目标target和拦截器this传给了包装函数。

3. setProperties(Properties properties)用于设置额外的参数，参数配置在拦截器的Properties节点里。

## 关于缓存

MyBatis将数据缓存设计成两级结构，分为一级缓存、二级缓存。

MyBatis查询数据的顺序是：二级缓存 ———> 一级缓存 ——> 数据库

![](https://upload-images.jianshu.io/upload_images/13390267-ad4faa357dcad444.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](https://upload-images.jianshu.io/upload_images/13390267-78fe30c54ed2c3fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 一级缓存 --- 会话级别的缓存

一级缓存位于SqlSession对象之中，又被称之为本地缓存。一级缓存是MyBatis内部实现的一个特性，用户不能配置，默认情况下自动支持的缓存，用户没有定制它的权利（不过这也不是绝对的，可以通过开发插件对它进行修改）。


##### SqlSession对象、Executor对象、Cache对象之间的关系

![](https://upload-images.jianshu.io/upload_images/13390267-a9dc2dc616965cba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 一级缓存被清除的情形：
1. MyBatis在开启一个数据库会话时，会创建一个新的SqlSession对象，SqlSession对象中会有一个新的Executor对象，Executor对象中持有一个新的PerpetualCache对象，当会话结束时，SqlSession对象及其内部的Executor对象还有PerpetualCache对象也一并释放掉。

2. 如果SqlSession调用了close()方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用；

3. 如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用；

4. SqlSession中执行了任何一个 update()、delete()、insert()，都会清空PerpetualCache对象的数据，但是该对象可以继续使用；

**一级缓存是一个粗粒度的缓存，没有更新缓存和缓存过期的概念。**


### 二级缓存 --- 应用级别的缓存
MyBatis中关于Cache接口的实现类也使用了装饰者设计模式

一个SqlSession对象会使用一个Executor对象来完成会话操作，MyBatis的二级缓存机制的关键就是对这个Executor对象做文章。如果用户配置了"cacheEnabled=true"，那么MyBatis在为SqlSession对象创建Executor对象时，会对Executor对象加上一个装饰者：CachingExecutor，这时SqlSession使用CachingExecutor对象来完成操作请求。CachingExecutor对于查询请求，会先判断该查询请求在二级缓存中是否有缓存结果，如果有查询结果，则直接返回缓存结果；如果缓存中没有，再交给真正的Executor对象来完成查询操作，之后CachingExecutor会将真正Executor返回的查询结果放置到缓存中，然后在返回给用户。

##### 二级缓存的配置

1. mybatis全局配置文件中的setting中的cacheEnabled需要为true(默认为true，不设置也行)
2. mapper配置文件中需要加入<cache>节点
3. mapper配置文件中的select节点需要加上属性useCache需要为true(默认为true，不设置也行)

##### cache-ref节点
mapper配置文件中还可以加入cache-ref节点，它有个属性namespace。如果每个mapper文件都是用cache-ref，且namespace都一样，那么就代表着真正意义上的全局缓存。如果只用了cache节点，那仅代表这个这个mapper内部的查询被缓存了，其他mapper文件的不起作用，这并不是所谓的全局缓存。

#### 缓存的细粒度
MyBatis并不是简单地对整个Application就只有一个Cache缓存对象，它将缓存划分的更细，即是Mapper级别的，即每一个Mapper都可以拥有一个Cache对象，具体如下：

1. 为每一个Mapper分配一个Cache缓存对象（使用<cache>节点配置）
MyBatis将Application级别的二级缓存细分到Mapper级别，即对于每一个Mapper.xml,如果在其中使用了<cache> 节点，则MyBatis会为这个Mapper创建一个Cache缓存对象，如下图所示：

![](https://upload-images.jianshu.io/upload_images/13390267-409bc28500f4aad3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 多个Mapper共用一个Cache缓存对象（使用<cache-ref>节点配置）
如果你想让多个Mapper公用一个Cache的话，你可以使用<cache-ref namespace="">节点，来指定你的这个Mapper使用到了哪一个Mapper的Cache缓存。

![](https://upload-images.jianshu.io/upload_images/13390267-1c638f23e5e8e450.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## MyBatis数据源
MyBatis把数据源DataSource分为三种：在environments的子节点dataSource中可以配置数据源。

![](https://upload-images.jianshu.io/upload_images/13390267-a8ece4d13248a5b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	<dataSource type="POOLED">
	  <property name="driver" value="${driver}"/>
	  <property name="url" value="${url}"/>
	  <property name="username" value="${username}"/>
	  <property name="password" value="${password}"/>
	</dataSource>

## MyBatis事务管理机制
<environment>节点定义了连接某个数据库的信息，其子节点<transactionManager> 的type会决定我们用什么类型的事务管理机制。

1. 使用JDBC的事务管理机制：即利用java.sql.Connection对象完成对事务的提交（commit()）、回滚（rollback()）、关闭（close()）等。
2. 使用MANAGED的事务管理机制：这种机制MyBatis自身不会去实现事务管理，而是让程序的容器如（JBOSS，Weblogic）来实现对事务的管理。

	
	
	