---
title:  《springboot开发实战》
date: 2019-11-08
categories: 
- springboot
tags: 
- 笔记
---

#                《springboot开发实战》



## 第三章 深入理解springboot boot自动配置

## 3.1传统的ssm开发过程

如果不指定 contextConfigLocation 就会使用XmlWebApplicationContext的默认配置及applicationContext.xml是spring的默认配置文件

springMVC上下文配置spring-MVC.xml其核心类是org.springframeworking.web.servlet.DispatcherServlet

```
<servlet>
	<servlet-name>springmvc</servlet-name>
	<servlet-class>org.springframeworking.web.servlet.DispatcherServlet<servlet-class>
	<init-param>
	<param-name>contextConfigLocation<param-name>
	<param-value>classpath:spring-mvc.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
```

web.xml的记载顺序：

```
<context-param>----》<listener>----》<filter>----》<servlet>
```

包扫描：

```
<context:component-scan base-package="com.easy.spring">
```

## 3.2 springboot 自动配置原理

### 3.2.1javaconfig

- 组件扫描（@ComponentScan）：扫描应用上下文Bean

- 自动装配（@Autowired）：依赖注入

- 启动mvc配置（@EnableMvcConfig）：启用mvc配置弹药继承WebMvcConfigurerAdapter

  

![webmvc1.png](https://i.loli.net/2019/09/26/JywVK6178IAHiEQ.png)

![webmvc2.png](https://i.loli.net/2019/09/26/C9EuwZpjiF4O5qV.png)



- 模板解析器，spring模板解析器，thymeleafVOewResolver Bean来配置ThymeleafVOewResolver
- 注册的ResourceHandlers Bean从classpath路径下配置uri/resources/**静态资源的请求映射到/resources/目录下
- 配置MessageSource Bean从ResourceBundle中的messages.properties消息配置文件中加载i18n

### 3.2.2条件化Bean

1. 条件注解@Conditional

   | 条件注解                        | 条件说明                 |
   | ------------------------------- | ------------------------ |
   | @ConditionalOnBean              | 存在某一对象就创建Bean   |
   | @ConditionalOnClass             | 位于类路径下创建一个Bean |
   | @ConditionalOnExpress           | 当表达式为true时候为Bean |
   | @ConditonalOnMissingBean        | 不存在某个对象时创建Bean |
   | @ConditonalOnMissingClass       | 不存在某类的时候创建Bran |
   | @ConditionalOnNotWebApplication | 不是web应用时            |

2. 调价注解使用实例

   2.1创建实例

   2.2实现Condition接口

​           实现org.springframework.context.annoation.Condition 接口，并重写match方法

```
public boolean matches(ConditionContext context ,AnnotatedTypeMetadate annotation)
```

 					match：第一个参数是ConditionalContext接口

​								    第二个参数类型AnnotatedTypeMetadata 检查@Bean上是否还有其他注解 ，通过方法

```
MutiValueMap<String,Object> attrs = metadate.getAllAnnotationAttributes(magicService.class.getName())
```

​	   2.3条件配置类

当满足自定义条件时候就注册@Bean下面的相应的类

![Conditional.png](https://i.loli.net/2019/09/26/n8Kfbcl3xY1aktd.png)

​		2.4 相应的业务逻辑

​		2.5 测试

### 3.2.3组合注解

将多个注解组合为一个注解，使用启动类注解就是组合注解。

```java
@1
@2
@3
public @interface Kirkzhang
```

## 3.3 springboot自顶配置

核心注解：@Import（EnableAutoConfigurationImportSelector.class）
@EnableAutoConfiguration

具体是实现是使用了spring原有的工具类SpringFactoriesLoader实现自动配置

### 3.3.1 @EnableAutoConfiguration注解

```mermaid
graph LR
A[EnableAutoConfiguration注解]--导入EnableAutoConfigurationImportSelector读取-->B[spring.factories下的key]
```

下key为EnableAutoConfiguration的对应类全限定名

关键代码是：EnableAutoConfigurationImportSelector的方法

```
protect List<String> getCondidatateConfiguration（AnnotationMeta meta）{

	List<String>configuration = SpringFactoriesLoader.loadFactoryName(
	getSpringFactoriesLoaderFactoryClass(),getBeanClassLoader()
    )
}
getSpringFactoriesLoaderFactoryClass()会去spring.factories文件过滤出EnableAutoConfiguration的所有实现类
```

实现一个spring.factories的指定实现类，标上@Configuration的注解就可以实现一个自己的starter

### 3.3.2 spring.factories文件

里面是配置文件

### 3.3.3 配置候选类

根据@Condiation注解来加载相应的Bean

## 3.4FreeMakerAutoConfiguration

### 3.4.1spring-boot-starter-freemarker工程

pom.xml和spring.provides

![1569670168840](D:\Documents\Markdown文档\springboot\1569670168840.png)

![](https://gitee.com/kirk_zhang/KirkzhangBlog/raw/master/images/springboot/starter原理.png)

自动传递到autoconfig工程中

入口类：FreemarkerAutoConfiguration

@Configuration

@ConditionalOnClass

@EnableConfigurationProperties

表示启动对内嵌配置类的支持

