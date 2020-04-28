# lec02: RPC_and_Thread

## Remote Procedure Call(RPC)

### 2.1.1 RPC主要目的

- 容易编写网络通信协议
- 隐藏客户端服务器通信细节
- 客户端调用更像传统过程
- 服务端处理过程更像传统处理过程

### 2.1.2 理想上处理通信就像函数调用一样

- client：

  ```
  z=f(x,y)
  ```

- server

  ```
  f(x,y){compute return z}
  ```

### **2.1.3 notes**

- 调用哪些服务器函数
- 序列化：格式化数据到包中
  - 棘手的数组，指针，对象等
  - GO的RPC库非常强大
  - 有些东西不能传递channel和function
- 绑定：客户端怎么知道跟谁通信
  - 也许客户端使用服务器的hostname
  - 也许使用命名服务，将服务名字映射到服务器
- 线程：
  - 客户端可能使用多线程，所以多一个调用没被处理，对应的处理器可能会很缓慢，所以服务器经常将每一个请求放置在单独的线程内

### **2.1.4 RPC怎么处理失败**

- 比如：丢包，网路断线，服务器运转慢，服务器运转崩溃
- 客户端没有获得服务器响应，服务器不知道客户端请求，
- 解决办法：
  - 超时重传

#### **2.1.4.1简单的方案：“最少一次的”：**

- 超时重传
- 不适用所有场景，如果是减库存操作，只读操作是可以用这个策略的，server可以自己处理重复写过程

#### **2.1.4.2更好的RPC行为：“最多一次”**

- IDEA:服务器的rpc代码发现重复的请求，返回之前的回复，而不是重写

- 如何发现重复请求，xid唯一标识。

  **Pivotal:最多一次的复杂性：**

  - 如何保证RPC序列号唯一性
  
  - 服务器必须忽律旧的RPC，如何安全的忽律
    - 唯一RPC序列号。类似于tcp请求的ack包
    ```
    client让每一个请求带有唯一标示码XID(unique ID),相同请求使用相同的XID重新发送。   server：     
    if seen[xid]:         
    	r = old[xid]      
    else          
    	r = handler()          
    	old[xid] = r          
    	seen[xid] = true
    ```
    - 每次只允许有一个未完成的RPC,收到seq+1的请求超时就忽律所有<=seq的请求
    - 客户端只维持五分钟时间，服务器自动忽律超过五分钟的请求
    
  - 当一个请求还没处理完时，如何处理重复请求
  
    - 每个RPC请求添加一个“pending”标志
    - 继续忽律或者等待
  
  - 如果执行“最多一次”策略时宕机并重启怎么办？
  
    - 可能需要把这些数据写入磁盘
    - 可能备机也需要这些数据
  
  - “exactly once”怎么样?
    等于”at most once” + 无限次重试 + 容错服务

### 2.1.5 Go RPC使用的是”at-most-once”

1. 打开TCP链接

2. 将请求写入TCP

3. TCP可能会重传，但是服务端会过滤

4. GoRPC代码不会重试（不会创建第二个TCP链接）

5. 如果没有收到回复就像应用返回错误

### 2.1.6 RPC概述

   - 更多的例子在test_test.go中
     比如： TestBasic()函数
   - 应用程序调用Call()函数，发生一个RPC请求并等待结果
     reply := end.Call("Raft.AppendEntries", args, &reply) 
   - 服务器端
     srv := MakeServer()
       srv.AddService(svc) // 一个服务器含有多个服务, 比如. Raft and k/v
       svc := MakeService(receiverObject) // 对象的方法将会处理RPQ请求



## thread

### 2.2.1 线程 = “控制线程”
+ 线程可以使一个程序同时执行很多事情
+ 线程共享内存
+ 每个线程包含额线程状态：程序计数器、寄存器、栈

### 2.2.2 线程挑战
+ 共享数据
	+ 两个线程在同一个时间修改同一个变量？
	+ 当一个线程读取数据，同时另一个线程正在修改这个数据？
	
    上面的问题经常被称为竞争，需要在共享数据上面使用Go中的sync.Mutex保护变量
+ 线程协调
	
	+ 比如：使用Go中的channels等待全部相关的线程完成工作
+ 死锁
	+ 线程1等待线程2
	+ 线程2等待线程1
	+ 比竞争容易诊断
+ 锁粒度
	+ 粗粒度 -->  	简单，但是更小的并发/并行
	+ 细粒度 -->	更好的并发，更容易数据竞争和死锁
+ 让我们一起看看名为labrpc的RPC包说明这些问题

### 结构
+ 网络结构
	+ 网络描述
		+ 服务器
		+ 客户端节点
	+ 每个Network结构都持有一个sync.Mutex

### 服务器结构
+ 一个服务器程序支持多个服务

### AddService
+ 添加一个服务名字
+ Q： 为什么上锁？
		AddService可能在多个goroutine中被调用
+ Q： defer() 函数的作用？
		deter的作用是在函数退出前，调用之后的代码，在里面就是添加完新服务后，做解锁操作。

### Dispatch
+ 分发请求到正确的服务
+ Q： 为什么持有锁？
		这里应该指的是Server::dispatch函数，
+ Q： 为什么不在函数的结尾处持有锁？

### Call():
+ 使用反射查找参数类型
+ 使用“gob”序列化参数(译注：gob是Golang包自带的一个数据结构序列化的编码/解码工具。)
+ e.ch是用于发生请求的通道
+ 需要一个通道从网上接收回复(<- req.replyCh)

### MakeEnd():
+ 使用一个线程或者goroutine模拟网络
	+ 从e.ch中获取请求，然后处理请求
	+ 每个请求分别在不同的goroutine处理
			Q: 一个端点是否可以拥有多个未处理的请求
    + Q:为什么使用rn.mu.Lock()?
    + Q:锁保护了什么?

### ProcessReq():
+ 查看服务器端
+ 如果网络不可靠，可能会延迟或者丢失请求，在一个新的线程中分发请求。
+ 通过读取e.ch等待回复直到时间过去100毫秒。100毫秒只是来看看服务器是否崩溃。
+ 最后返回回复
	Q： 谁会读取回复？
+ Q：ProcessReq没有持有rn锁，是否安全？

### Service.dispatch():
+ 为请求找到合适的处理方法
+ 反序列化参数
+ 调用方法
+ 序列化回复
+ 返回回复

### Go的内存模型需要明确的同步去通信
+ 下面的代码是错误的
	    var x int
    	done := false
    	go func() { x = f(...); done = true }
    	while done == false { }
   代码很容易写成上面，但是Go的语法会说没有定义，使用channel或者sync.WaitGroup替换

### 学习Go关于goroutine和channel的教程
+ 使用Go的竞争诊断器
+ [https://golang.org/doc/articles/race_detector.html](https://golang.org/doc/articles/race_detector.html)
    go test --race mypkg
