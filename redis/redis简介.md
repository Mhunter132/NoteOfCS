---
title:  redis简介
date: 2019-11-12
categories: 
- redis
tags: 
- redis
---
# redis简介

key里面存储filed-value的map类型

## redis数据结构

字符串类型

散列类型（Hash）

列表类型

集合类型

有序集合类型

key不要太长，最好不要超过1024个字节，但也别太短，要有一个统一的命名规范。value存储最长的字长是512M。

**int** 普通字符串不能用**incr**，**incrby**没有的key则会帮忙创建int类型，还可自定义加减。**decr , decrby**

**string** 还可以进行拼接，append key1 key2

**Hash** 存储复杂数据，**命令** 

设置value值 ：hset  myhash username zhangsan

査filed-value值：hgetall myhash 

设置多值：hmset myhash addr beijing  iphone 13213213213(hmset key value filed2 value2 ......给hash的key添加多个值)

取value值：hget key field

取多个值：hget key fileds

获取key的多个值：hgetall key

删除value:hdel key filed

删除整个list：del key

增加命令：hincrby key field increment

判断某个key是否存在：hexist key field

获取key包含field的数量：hlen key

获取所有的key：hkeys key

获取所有的value : hvals key

#### list数据类型

两端插入数据：lpush  key values ,如果没有对应 的key就创建对应的空链。

 在尾部添加元素  ： rpush  key values

查询范围内：lrange key  start end  （-1是最后一个元素，-2是倒数第二个元素）

左端弹出：lpop key

右端弹出：rpop key

获取列表中元素个数：llen key

向list左面插入，没有就不插：lpushx key value

向list右面插入，没有就不插：rpushx key value 

删除count个值为value的元素：lrem key count value.count大于零从头到尾遍历，count小于零从尾至头遍历。

删除所有数字value : lrem key 0 value

设置链表index下标的值 ：lset key index value (链表不存在则抛异常)

在pivot前或者后插入值 ： linsert key before|after pivot value

将链表尾部的值弹出并插入头部：rpoplpush resource destination。    resource尾巴给了destination

rpoplpush resource resource :自己弹插自己。(**可使用在消息队列场景**)

#### set数据类型

set数据类型不允许出现重复数据类型

添加/删除元素：sadd key values/srem key members

获得集合中的元素：smembers key

判断集合中指定指定成员是否在set中，在返回1，不在或者不存在返回0.

集合的差集运算：sdiff key1 key2(返回的是key1 -key2的结果，与key的循序有关）

集合的交集运算：sinter key1 key2 key3

集合的并集运算：sunion key1 key2 key3

获取set中成员数量：scard key

随即返回set中的一员：srandmember key

将key1 key2相差的元素存储在destination上：sdiffstore destination key1 key2

将key1 key2交集存储在destination上：sinterstore destination  key1 key2

将key1 key2并集存储在destination上：sunionstore destination key1 key2

使用场景：可以根据set的唯一性应用在ip地址上，或者购买某一个电子产品的用放在一起。

#### 存储sortedset

sorted-set和set类型极其相似，他们都是字符串的集合都不允许出现在同一个set之中。sortedset每一个成员都有一个**分数**关联来给成员排序。**分数** 是可以重复的，

添加元素：zadd key score member score1 member1 score2 member2

返回指定成员元素：zscore key member

获取集合中成员数量：zcard key

删除元素：zrem key members (移除集合中指定的成员，可以使多个成员)

范围查询：zrange key start end [withscores] 顺带返回分数

按照分数从大到小的循序防返回所以从start到stop之间的元素：zrevrange  key start stop

按照排名范围删除元素：zremrangebyrank key start stop 

按照分数范围删除元素：zremrangebyscore key min max

返回分数在【min max】的成员按照分数从低到高排序，【withscores】显示分数 【limit offset count】offset 表明从脚标为offset的元素开始返回count个成员 ： zrangebyscore key min max[withscores] [limit offset count]

设置指定成员增加的分数：zincrby key increment member

获取分数在【min, max】之间的成员：zcount key min max

返回成员在集合中的排名从小到大：zrank key member

返回成员在集合中的排名 ：zrevrank key member 

**可以应用于大型在线游戏的积分排名，排名，topN等等内容**

#### keys的通用操作

获取所有与pattern匹配的keys，* 表示任意一个或者多个。？表示一个：keys */keys my?

删除指定的key : del key1 key2 

判断key是否存在：exists key  存在返回1，不存在返回0

为当前key重命名：rename key newkey

为key设置过期时间 : expire key

获取key所剩的时间：ttl key

获取指定key的类型，并以字符串的形式返回，不存在返回none：type key

## jedis

创建配置文件properties文件，方便后期管理

```java
//获取properties文件方式，更灵活
ResourceBundle re = ResourceBundle.getBundle("properties文件名");
//有zh_CN就读zh_CN 没有就读默认的。
maxTotal=Integer.parseInt(re.getString("key");)
    ----------------------------------------------private static JedisPciolConfig;
    private static host;
    private static port;
    private static maxTotal；
    private static maxWaitMillis；
 static{
 	jc=new JedisPciolConfig（）；
     //获取properties中的配置数据
 	ResourceBundle:re = ResourceBurdle. ge tBundle（"jedis"）；
 	maxTotal=Integer . parseInt（re. getString（"maxTotal"））；
 	maxWaitMillis=：Long . parseLong（re . getString（ "maxWaitMillis"））；

	 host = re.getString（"host" ）；
	 port = Integer .parseInt（re.getString（"port"））；
	 //创建Jedis池
	  pool=new JedisPool（jc， host， port）； I
｝   //静态代码块不能调用非静态的数据,所以要定义成静态字段，静态方法。
public static Jedis getJedis(){
	 return pool.getResource();
}
```

```java
//硬编码的方式
@Test
public void testJedisPool（）{
//1获得连接池配置对象，设置配置项
JedisPoolConfig config = new JedisPoolConfig（）；
// 1.1最大连接数
config. setMaxTotal（30）；
// 1.2最大空闲连接数
config. setMaxIdle（10）；
//2获得连接池
JedisPool jedisPool = new JedisPool（config， "192.168.137.128"， 6379）；
//3获得核心对象
Jedis jedis = nu1l；
try {
jedis = jedisPool. getResource（）；
//4设置数据
jedis . set（"name"， "itcast"）；
//5获得数据
String name = jedis.get（"name"）；//name是key-value中的key
System. out. println（name）；
} catch （Exception e） {
e.printStackTrace（）；
} finally{
if（jedis ！= nu1l）{
jedis.close（）；
//虚拟机关闭时，释放pool资源
if（jedisPool ！= null）{
jedisPool. close（）；
}
```

### redis的特性

多数据库：redis和mysql一样允许多数据库。

订阅与发布消息：subscribe mychat ,订阅mychat频道

向指定的频道发布消息：publicsh mycaht '  ############  '

### redis事务

nosql也支持四五级制，但是是伪事务

1. 事务中的命令都会被串行执行，不会为其他的客户端提供任何服务，从而保证原子性
2. redis一条命令执行失败，其他的命令还是会执行成功
3. mutil表明开启事务，exec/discard表明提交和回滚事务，
4. 如果任务队列在执行过程中被断电请情况打断，后面的命令将不会被执行。如果网络中断发生在客户端执行exec之前则所有命令都会被执行。
5. 当使用appendonly模式时，redis会通过调用系统函数将本次所有写操作写入硬盘，发生宕机会写一半，redis会在重启时执行一致性检查，我们需要利用redis-check-aof工具，该工具帮助我们定位不一致的错误。

### redis持久化

将内存中的数据同步到硬盘中的过程叫持久化，

##### rdb持久化：默认支持在指定间隔时间将数据集快照写入硬盘

##### aof持久话：以日志的形式记录服务器所处理的每一个写操作，redis重启就会重构数据库，保证完整。

##### 无持久化：就相当于与一个memcached

##### redis可以同时使用rdb和aof

### RDB

优势

1. 一个数据库就一个文件
2. 可以转移到其他服务器上进行解压
3. 持久花是异步进行避免集中的io操作
4. 相比aof如果数据量很大，rdb会有很高效率

劣势

1. 如果想保证数据的高可用，应该避免数据的丢失，持久化之前发生的宕机数据都会丢失。
2. 因为数据库是通过fork一个子进程进行io，如果数据量大会导致服务停止数秒。

##### 配置说明Snapshotting

save900 1 #每900秒（15分钟）至少有1个key发生变化，则dump内存快照。
save300 10 #每300秒（5分钟）至少有10个key发生变化，则dump内存快照
save 60 10000 #每60秒（1分钟）至少有10000个key发生变化，则dump内存快照

### AOF

优势

1. 有更高的数据安全性，提供每秒同步，每次修改同步，和不同步，每次修改同步是效率最低的
2. 因为是采用append模式，每次写入都是不会破坏以前的内容，如果发生数据一致性问题，可以使用工具来帮助姜茶
3. append模式会自动启用rewirite机制，在对老文件修改的同时，也会创建新文件来记录哪些命令被修改。

劣势

1. 相同数量aof通常会大于rdb
2. 根据同步策略不同，aof在运行效率上玩玩慢于rdb,每秒同步效率很高，禁用同步的效率和rdb同样高

#### 配置AOF

5.3.3.1配置信息
always #每次有数据修改发生时都会写入AOF文件
everysec #每秒钟同步- -次，该策略为AOF的缺省策略
 no #从不同步。高效但是数据不会被持久化，

重写AOF：若不满足重写条件时，可以手动重写，命令：bgrewriteaof