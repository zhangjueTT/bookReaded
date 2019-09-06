redis是一个键值对数据库，可以通过redis-cli启动命令行工具，命令可以大小写混写，建议都大写。

## Redis Strings:


	set key value [EX seconds] [PX milliseconds] [NX|XX]
	
	get key
	
	INCR key
	
	INCRBY key increment
	
	DECR key
	
	DECRBY key decrement
	
	//设置新值，并返回原值
	GETSET
	
	MSET key value [key value ...]
	MGET key [key ...]
	
	
	exists key [key ...]
	
	DEL key [key ...]
	
	type key 
	
	expire key seconds
	
	ttl key


## Redis lists基于Linked Lists实现。

	LPUSH key value [value ...]
	RPUSH key value [value ...]
	rpop key
	lpop key
	
	// -1 代表尾部元素
	lrange key start stop  
	LLEN key
	
	
## Redis hash
	
	hset key field value
	hmset key field value [field value ...]
	hget key field
	hmget key field [field ...]
	
	hgetall key
	
	// 居然只有加没有减
	hincrby key field increment
	
	
	Redis Set 是 String 的无序排列
	
	sadd key member [member ...]
	smembers key
	sismember key member
	spop key [count]
	
	// 获取set的数量
	scard key

	// 它获取不同 set 的交集
	sinter key [key ...]
	
	// 用于对多个集合取并集，并把结果存入另一个 set 中
	SUNIONSTORE destination key [key ...]



## Redis Sorted sets
和set一样值唯一，但是需要给每一个值设置一个分数（这里类似hash），按分数排序。规则如下。

- If A and B are two elements with a different score, then A > B if A.score is > B.score.
- If A and B have exactly the same score, then A > B if the A string is lexicographically greater than the B string. A and B strings can’t be equal since sorted sets only have unique elements.

命令：

	zadd key [NX|XX] [CH] [INCR] score member [score member ...]
	
	zrange key start stop [WITHSCORES]
	zrevrange key start stop [WITHSCORES]
	
	// -inf, inf  可用
	zrangebyscore key min max [WITHSCORES] [LIMIT offset count]
	
	// remove data between min and max 
	zremrangebyscore key min max
	
	//  ask what is the position of an element 
	zrank key member
	
	
exicographical scores  字典顺序分数,当sorted set 的分数相同时，采用字典序。
	
	zrangebylex key min max [LIMIT offset count]

















