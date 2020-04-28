#  第一章_spring介绍

## 1.1 依赖注入和控制反转

依赖关系：app中pojo对象彼此之间的的关系

传统构建app：我们要自己设计写设计模式，管理pojo之间关系，各种组件都需要自己写，完全就是from zero to hero式的开发，故周期长，对开发人员要求也高。

spring: 我提供各种你开发可能用到的组件，剩下的具体细节根据自己业务情况自己开发。我们只给你锅碗瓢盆，但是做什么菜你自己来。

IOC: invert of  control ,说白了就是由spring帮忙创建对象，具体怎么创建的？自己看源码。

DI:  马丁福乐说：“不行啊，这名字不够直观，看着就像是把对象注入进引用里面的"

其实Ioc和Di是两个概念但是是描述的一件事。

## 1.2 模块

![1587985822124](E:\note_book_of_computer_science\spring文档阅读\1587985822124.png)

- core(spring基础)
- beans(spring基础)
- context
- support
- expression

### 1.2.1核心容器

- **spring-core**和**spring-beans**模块提供了框架的基础功能。**beanFactory**是成熟的工厂模式实现，帮忙完成到单例模式创建，context就像是JNDI注册表（**JNDI全称是java naming and directory interface。简单点就是你按命名规则给一个东西命名然后你就可以通过该名字在特定环境下直接查找到该东西了**）

- **ApplicationContext**接口是**Context**模块的核心(ApplicationContext是核心接口)
- **spring-context-support**支持整合普通第三方库到Spring应用程序上下文，用于高速缓存（ehcache，JCache）和调度（CommonJ，Quartz）的支持（将第三方组件支持到context）。
- **spring-expression**模块提供了强大的表达式语言去**支持查询和操作运行时对象图**（操作运行时对象图）

### 1.2.2 AOP和Instrumentation

在**spring-AspectJ**允许定义方法拦截器和切入点（**pointcuts**），以便实现业务之间解耦。

### 1.2.3 消息

Spring框架4包括**spring-messaging(**消息传递模块)，其中包含来自Spring
Integration的项目，例如，**Message，MessageChannel，MessageHandler，等用来传输消息的基础应用**。该模块还包括一组用于将**消息映射到方法**的注释
(annotations)，**类似于基于Spring MVC注释的编程模型**。

### 1.2.4 数据访问/集成

- **spring-jdbc**模块提供了一个JDBC –抽象层，消除了需要的繁琐的**JDBC**编码和数据
  库厂商特有的错误代码解析。

- **spring-tx**模块支持**声明式事务和编程式事务**

- **spring-orm**模块为**对象关系映射**(object-relational mapping )API提供集成
  层，包括JPA和Hibernate。

- **spring-jms**模块(Java Messaging Service) 包含用于生产和消费消息的功能

### 1.2.5 web

- **Web层由spring-web，spring-webmvc和spring-websocket** 模块组成,会初始化一个**servlet**监听器和面向web的**IOC容器**，它还包含**一个HTTP客户端**和**Spring的远程支持的Web相关部分**。

- **spring-webmvc模块（**也称为Web-Servlet模块）包含**SpringMVC**和**REST Web Services实现**

### 1.2.6 测试

**spring-test模块支持使用JUnit或TestNG**对Spring组件进行单元测试和 集成测试。
它提供了**Spring ApplicationContexts的一致加载和这些上下文的缓存**。它还提供可
用于独立测试代码的模仿(mock)对象。

## 1.3 使用场景

不懂文档中场景

### 1.3.1 依赖管理和命名约定（spring-*.jar）

将功能集成到项目中,spring**运行时**将模块导入到类路径（classpath）中，也可能在**编译时**就需要加入类路径,是管理文件系统中的物理资源，这些是直接关系，间接依赖关系具有“传递性”（例如我的应用程序依赖于commons-dbcp ，而commons-dbcp 又依赖于commons-pool），它们是最难识别和管理的依赖关系，spring是你用什么就导什么。

### 1.3.2 maven/gradle 

这些属于工具的使用

### 1.3.3 日志

​	Spring中的强制性日志依赖关系是Jakarta Commons Logging API（JCL）。我们针对JCL进行编译，使JCL Log 对象对于Spring Framework的类可见。

​	对于用户来说，**所有版本的Spring都使用相同的日志库很重要：迁移很简单，**因为即使扩展了Spring的应用程序，但仍然保留了向后兼容性。我们这样做的方式是使Spring中的一个模块显式地依赖于commons-logging（遵循JCL规范的实现）。

​	commons-logging 的好处在于，您不需要任何其他操作，应用程序依然能正常工
作。它具有运行时发现算法，可以在类路径中查找其他日志框架

**不使用JCL,排除common-logging**

![1587990195657](E:\note_book_of_computer_science\spring文档阅读\1587990195657.png)

- 第一选择可能是Simple Logging Facade forJava（**SLF4J是接口**），它也被许多其他使用Spring的应用工具所使用。
  基本上有两种方法来关闭commons-logging：

  - 排除spring-core模块的依赖关系（因为它是明确依赖于commons-logging的唯
    一模块）

  - **使用SLF4J是一个更清洁的依赖关系，在运行时比commons-logging更有效率，因为它**
    **使用编译时绑定，而不是其集成的其他日志框架的运行时发现**（说白就是运行时绑定不是编译器去找）
  - 要使用SLF4J与Spring，您需要使用SLF4J-JCL bridge替换commons-logging依赖关系。一旦完成，那么在Spring中日志调用将被转换为对SLF4J API的日志调用，因此如果应用程序中的其他库使用该API，那么您需要有一个统一的地方来配置和管理日志记录
  
  
  
- 使用的**Log4j**
  -  许多人使用Log4j 作为配置和管理日志的日志框架。它是高效和成熟的，当我们构
    建和测试Spring，**实际上它是在运行时使用的**。 Spring还提供了一些用于配置和初
    始化Log4j的实用功能，因此它在某些模块中对Log4j具有可选的编译时依赖性。
    要使用JCL和Log4j，所有你需要做的就是把Log4j加到类路径，并为其提供一个配
    置文件（log4j2.xml，log4j2.properties或其他 支持的配置格式）。对于Maven用
    户，所需的最少依赖关系是
  
- （补充）Logback

- Log4j2