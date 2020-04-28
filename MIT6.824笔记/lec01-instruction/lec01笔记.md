# 6.824 第一节课：介绍

## 6.824：分布式系统工程

### 1.1  什么是分布式系统？

- 多台计算机协同工作，有的书定义为（ 多台协同计算的集合）
  - 应用：网络应用层DNS系统p2p文件共享
- 大多数基础设施都是分布式

### 1.2 为什么需要分布式

  **4优点：**

  - 为了**处理大量数据**，通过增加机器来提高处理数据的效率

  - 通过**隔离来提高安全性**，比如有些需要强一致性的系统

  - 通过复制实现**容错**（replication）

  - **方便扩容**

  **3缺点：**

  - **并发部分难**实现

  - **必须要处理失败**情况

  - **难实现最高性能**（很难压榨系统最高性能）

  **为什么选择这门课：**(我是因为这门课是牛逼老师讲的我能学到很多)

  - 兴趣 -- 难题, 非显而易见的解决方案(non-obvious solutions)
  - 被实际系统使用 -- 被大网站的崛起而驱动大网站的崛起
  - 活跃的研究领域 -- 快速进步的领域 和 有大量问题没有解决的领域
  - 动手做 -- 你讲通过实现建立多个系统

### 1.3 课程涉及抽象概念，阅读材料和实验

**阅读：研究论文作为案例研究**

- **课前一定要阅读论文**

- **实验目标**

  - 深入理解分布式技术
  - 掌握分布式编程经验

- **实验安排**

  - Lab 1: MapReduce
  - Lab 2: replication for fault-tolerance
  - Lab 3: fault-tolerant key/value store
  - Lab 4: sharded key/value store

- **概念抽象（重点概念）：**
- **存储**（storage）
  - 通讯（communication）
  - 计算（computation）
  - RPC、thread、concurrency control
  
- **性能：**
  - 可伸缩架构，添加机器提高性能
  - 负载均衡：straggler问题（五条原因：：资源的竞争、硬件的可靠性（主要指磁盘可靠性）、网络带宽的差异性、网络堵塞）
  - "Small" non-parallelizable parts。
    隐藏共享资源等，还有网络问题。案例学习

- **容错：**
- 上千台计算机，复杂网络，一定会出错，出问题
  - exp：十台机子有五台更新最新的值，但是剩下五台都没更新
  - 一致性和性能不可兼得
    - 需要强一致性的系统往往很慢（牺牲性能，要强一致性）
    - 需要性能牺牲一些强一致性

### 1.4 MapReduce论文的学习

  原理图

  	![img](https://images2015.cnblogs.com/blog/1110462/201703/1110462-20170330105300154-129489662.png)

  

MapOutput是中间数据，谷歌防止中间数据网络传输，将Mapper和Reducer和文件系统放在一起，

mapper并行的读取数据（key/value形式）比如key是文件名，value是文档内容，

~~~
map（string key,string value）:
	//key 是文件名
	//value是 文档内容
	for each word in value：
		然后输出中间文件。也是key/value的形式
		
reduce(string key,string value):
	//key :a word
	//value: a list of counts
	for each v in value:
		result += parseint(v):
 论文中举了一个计算词评的例子，map读取大量文档内容，然后输出中间数据（一行一个key/value堆积数据）key是关键词，value是该关键词在某一行在某一篇文章出现多少次，然后reduce函数开始计算关键词key的数量
~~~

~~~java
原理缩率图
	Input Map -> a,1 b,1 c,1
    Input Map ->     b,1
	Input Map -> a,1     c,1
  	              |   |   |
      	              |   -> Reduce -> c,2
          	          -----> Reduce -> b,2
~~~



**谷歌场景举例（加深理解计算模型）：**

1. 分布式字符串查找
	map函数输出一个<key,value>键值对，如果该键值对匹配字典中的模式串，这是reduce函数就将该键值对复制走
	
2. url接入频率计算
	map函数处理web请求也的log日志，输出<url,1>key/value中间值，然后reduce函数会根据这个url关键字，收集计算这个url数量输出<url,count of url>
	
3. 翻转web-link图（web页关系图）
	map输出<target，source>map中间值，source为网页网页名字，target是跳转到的目标网页，reduce函数会输出<target，list（source)>数据，（这个数据作为权重方便计算网页重要性）
	
4. 排序文档
	map函数输出<word,document ID>然后reduce函数根据word计算输出<word，list（doucment id）>
   

   
### 1.5 MapReduce模型的好处

- **易扩展**：隐藏了很多的具体并行的并发的实现，使程序员可以很轻松的实现大数据集处理，mapper和reducer函数可以并行
- **容错**：如果worker执行到一半出错，则改work以前完成的任务作废(置为空闲)，然后等待master重新调度，重新执行，master负责给worker分配任务，调度worker，读取分隔的数据，输出三分拷贝大小在【16-64M】
  - Map函数执行失败，这时切换worker，之前worker执行的数据全部置空，如果reduce获取全部函数，那么master不会继续调用map
  - ｍaster怎么知道work崩溃？(master定时ping worker)
  - reduce在输出前崩溃，切换worker，如果在过程中崩溃，那么会重命名输出
- **性能(谷歌如何实践的)**还有哪些设计可以提高网络性能
  - Map的输入来自本地磁盘而不是来自网络
  - 中间值只在网络上传输一次，保存在本地磁盘而不是GFS
  - 中间值别划分多个分区，大网络传输更加有有效
- 怎样处理**负载均衡**
  
  - 多加worker（很暴力的方法，哈哈哈）
- **其他问题**
  - **意外开始两个相同worker，也会执行一个map,如果两个reduce处理同一份文件，那么GFS会触发重命名院子操作（只执行一个map，reduce只触发一个名字）**
  - 假如一个Map函数很慢怎么办：**硬件肯能很糟糕**，master会对这些最后的任务创建第二份拷贝任务执行。
  - 假如一个worker因为软件或者硬件的问题导致计算结果错误怎么办？
    太糟糕了！MR假设是建立在"fail-stop"的cpu和软件之上。
  - 假如master崩溃怎么办？客户端要重新启动(**我也有疑问，为什么不做成主备形式**)
### 1.6 Mapreduce的缺点

  - **小的数据**，因为管理成本太高,如非网站后端
  - **大数据中的小更新**，比如添加一些文件到大的索引
  - **不可预知的读(Map 和 Reduce都不能选择输入）**
  - **Multiple shuffles**, e.g. page-rank (can use multiple MR but not very efficient)（**多点复制**，一map对多reduce）
  - 多数灵活的系统允许MR，但是使用非常**复杂的r模型**

 应该都是单点的，根据 https://stackoverflow.com/questions/5813665/fault-tolerance-in-mapreduce 和 第 36页https://www2.cs.duke.edu/courses/spring16/compsci516/Lectures/Lecture-14.pdf









   
