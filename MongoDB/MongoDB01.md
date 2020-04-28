# MongoDB

## 问题：

在一个 10 万数据量的 list 中，我只需要 
满足特定条件的元素在 Redis 中，使用集合或者列表，你只有先把元素取出，然后才能通过条件筛选 ，一个个得到你想要的数据，这显然存在比较大的问题。对于那些需要缓存而且经常需要，统计、分析和查询的数据 对于 Redis 这样简单的 NoSQL 显然就不是那么便捷了 这时另外一
NoSQL 就派上用场了，它就是本章的主题MongoDB,对于那些需要统计、按条件查询和分析的数
据，它提供了支持，它可以说是一个最接近于关系数据库的 NoSQL

## 定义：
MongoDB 是由 c＋＋语言编写的一种 NoSQL ，是 个基于分布式文件存储的开源数据库系统。
在负载高时可以添加更多的节点，以保证服务器性能， Mo goDB 的目的是为 We 应用提供可扩展的 
高性能数据存储解决方案。 MongoDB 将数据存储为 个文档，数据结构由键值（ key-value ）对象组成。 这里的 Mongo DB 文档类似于 JSON 数据 （BSON），所以很容易转化成为 Java POJO 对象或者 JavaScript 对象，这些字段值还可 以包含其他文档、数组及文档数组。

**依赖**

```xml
<dependency> 
	<groupid>org.springfrarnework.boot</groupid> 
	<artifactid>spring-boot-starter-data-mongodb</artifactid>
</dependency> 
<dependency> 
<groupid>com.alibaba</groupid> 
	<artifactid>fastjson</artifactid>
	<version>l.2.39</version> 
</dependency>
```

**配置清单**

```xml
# MONGODB (MongoProperties) 
spring.data.rnongodb.authentication database= 		#用于签名的 Mon go DB 数据库
spring.data.rnongodb.database=test 					#数据库名称
spring.data.rnongodb.field-narning-strategy= 		#使用字段名策略
spring.data.mongodb.grid-fs-database=   			# GridFs （网格文件〕数据库名称
spring.data.mongodb.host=localhost 					# MongoDB 服务器 不能设置为 URI
spring.data.mongodb.password= 						# MongoDB 服务器用户密码 不能设置为 URI
spring.data.mongodb.port= 							# MongoDB 服务器端口 不能设置为 URI
spring.data.mongodb.repositories.type=auto 			#是否启用MongoDB 关于 JPA 规范的编程
spring.data.rnongodb.uri=rnongodb://localhost/test 	# MongoDB 默认 URI
spring.data.mongodb.usernarne= 						# MongoDB 服务器用户名，不能设置为 URI
```

Spring Boot 自动创建的 MongoDB 相关的 Bean

```
MongoClient							MongoDB 客户端
MongoProperties						SpringBoot关于MongoDB的自动配置属性
```

