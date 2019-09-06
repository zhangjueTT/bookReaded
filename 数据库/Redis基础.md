![](https://upload-images.jianshu.io/upload_images/13390267-de07d87926200191.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

redis 的源码都是用C语言写的，而且，整个redis的大小都很小。本文主要参考redis设计与实现一书，书中部分数据与最新的redis源码有所出入，但不影响我们的使用学习。

## 基本类型：

建议这里的学习大家去参看一下源代码，或者相应的书籍，本节不对具体的细节做讨论，仅总结没种数据类型的作用。

##### 1.动态字符串

	struct sdshdr{
		// 记录字符长度，不计 “\0”
		int len;
		// 剩余空间
		int free;
		// 字符数组
		char buff[];
	}

重定义c语言中的字符串，使得字符串的大小可以改变，减少因为字符串大小改变导致的频繁释放内存，分配内存。以适应数据库的使用场景。
##### 特点：
	- 惰性空间释放
	- 避免过多的字符串
	- 兼容底层的c字符串

##### 2.链表
redis 内置实现了链表的结构。

	typedef struct listNode {
	    struct listNode *prev;
	    struct listNode *next;
	    // 定义void指针是因为可以指向任何类型
	    void *value;
	} listNode;
	
	
	typedef struct list {
	    listNode *head;
	    listNode *tail;
	    //复制函数
	    void *(*dup)(void *ptr);
	    // 释放函数
	    void (*free)(void *ptr);
	    // 节点值对比函数
	    int (*match)(void *ptr, void *key);
	    // 节点数量
	    unsigned long len;
	} list;
 
##### 3.字典

键值对，redis使用时的键值关系就是靠它了！

	typedef struct dictht {
		// hash表数组
	    dictEntry **table;
	    unsigned long size;
	    // 计算索引值的掩码，大小为 size - 1
	    unsigned long sizemask;
	    // 已使用节点数
	    unsigned long used;
	} dictht;
	
	typedef struct dictEntry {
		// 键值对
    	void *key;
	    union {
	        void *val;
	        uint64_t u64;
	        int64_t s64;
	        double d;
	    } v;
	    struct dictEntry *next;
	} dictEntry;
	
	// 字典中自带了两个hash表，一个平常使用，一个rehash的时候使用。rehash不是一次完成的，而是渐进完成的。
	typedef struct dict {
	    dictType *type;
	    void *privdata;
	    dictht ht[2];
	    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
	    int iterators; /* number of iterators currently running */
	} dict;
	
还有一些具体的类型定义等，可以参考源代码。

##### 4.跳跃表
跳跃表是一个有序的结构，先安分数排序，再按对象大小排序，听起来是不是很像sorted set，我猜就是这种数据的底层结构。一个跳跃表的例子：

![](https://upload-images.jianshu.io/upload_images/13390267-f40e1e9182864b04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 5.整数集合
该数据结构可以很便利的用于存取数据库中的整数集合。

	typedef struct intset {
	    uint32_t encoding;
	    uint32_t length;
	    int8_t contents[];
	} intset;
	
redis的整数集合还会有一定的升级和降级的操作以节省内存。

##### 6.压缩列表
为了节约内存而开发的，对于可以压缩的小数值，小的字符串等进行键的压缩。

##### 7.对象
redis基于以上各种数据结构，建立相应的对象结构，从而以对象构建数据库。对象包括：字符串对象，列表对象，集合对象，有序集合对象，哈希对象。除此之外，redis还引入引用计数和内存回收机制。

##### 8.数据库

	typedef struct redisDb {
	    dict *dict;                 /* The keyspace for this DB */
	    dict *expires;              /* Timeout of keys with a timeout set */
	    dict *blocking_keys;        /* Keys with clients waiting for data (BLPOP) */
	    dict *ready_keys;           /* Blocked keys that received a PUSH */
	    dict *watched_keys;         /* WATCHED keys for MULTI/EXEC CAS */
	    struct evictionPoolEntry *eviction_pool;    /* Eviction pool of keys */
	    int id;                     /* Database ID */
	    long long avg_ttl;          /* Average TTL, just for stats */
	} redisDb;

- struct redisServer 
- typedef struct client

定义了客户端和服务器。

## Redis 持久化
你可以理解为mysql一样的在内存留下的数据。

### 1.RDB 持久化
一定间隔内将内存的数据保存的本地的RDB文件中，它保存了某个时间点得数据集,非常适用于数据集的备份。

优点：与AOF相比,在恢复大的数据集的时候，RDB方式会更快一些。
缺点：万一在Redis意外宕机, 你可能会丢失间隔时间内的数据.

### 2.AOF 持久化
一个只进行追加的日志文件，记录下每次执行的命令，复原的时候把所有命令执行一遍，不改变数据以及运行状态的命令不记录。过大的时候整理重写。

优点：备份时间极短。数据丢失量小。
缺点：AOF 文件的体积通常要大于 RDB 文件的体积。

关于持久化的详细信息可参考[官网指南](http://www.redis.cn/topics/persistence.html)


## 事务
MULTI 、 EXEC 、 DISCARD 和 WATCH 是 Redis 事务相关的命令。事务可以一次执行多个命令， 并且带有以下两个重要的保证：

- 事务是一个单独的隔离操作：事务中的所有命令都会序列化、按顺序地执行。事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。
- 事务是一个原子操作：事务中的命令要么全部被执行，要么全部都不执行。


#### Redis 不支持回滚（roll back）：

Redis 命令只会因为错误的语法而失败（并且这些问题不能在入队时发现），或是命令用在了错误类型的键上面：这也就是说，从实用性的角度来说，失败的命令是由编程错误造成的，而这些错误应该在开发的过程中被发现，而不应该出现在生产环境中。

因为不需要对回滚进行支持，所以 Redis 的内部可以保持简单且快速。

有种观点认为 Redis 处理事务的做法会产生bug， 然而需要注意的是， 在通常情况下， 回滚并不能解决编程错误带来的问题。 举个例子， 如果你本来想通过 INCR 命令将键的值加上 1 ， 却不小心加上了 2 ， 又或者对错误类型的键执行了INCR ， 回滚是没有办法处理这些情况的。

本人对于redis的 实操尚少，更多的知识来自书籍和官网，本文尚介绍部分redis的特性，更多的学习可以参考[官网文档](http://www.redis.cn/documentation.html)

