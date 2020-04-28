---
title:  springboot笔记
date: 2019-11-08
categories: 
- springboot
tags: 
- springboot注解
---
# springboot入门

# 1.入门

## 	1.1去官网可以快速构建依赖

​	<  https://start.spring.io/ >

## 	1.2构建maven项目

# 2.@SpringBootApplication及相关注解

**@SpringBootApplication： **【入口类】@SpringBootApplication注解会扫描同级目录和子目录的组件类到Spring容器。

### 	2.1注意事项

​	SpringBootApplication也可以指定扫描的包位置，SpringBootApplication(scanBasePackage={"cn.------.--"})

## 	2.2 热部署

​	我们将修改完代码开发工具自动编译的过程称为，热启动。Spring boot是支持热启动的。只有加入以下依赖就可以

```xml
<dependency>  
       <groupId>org.springframework.boot</groupId>  
       <artifactId>spring-boot-devtools</artifactId>  
       <!-- 
           optional=true,依赖不会传递，该项目依赖devtools；
           之后依赖该项目的项目如果想要使用devtools，需要重新引入 
       -->
       <optional>true</optional>  
</dependency>
```

​	重点是spring-boot-autoconfigure包,因为spring boot的所有	内置的自动配置的类都在里面！

## 	2.3注解属性说明

## 		2.3.1basePackages属性

​		@SpringBootApplication默认扫描的范围是使用该注解的当前的类的包以及子包,如果要指定其他范围的包，可以是basePackages指定。

### 		2.3.2.basePackageClasses属性

​		用于精确指定哪些类需要创建对象加载到Spring容器里面。

### 		2.3.3.exclude属性

​		通过Class的方式排除不扫描的类，就是该类不创建对象。

### 		2.3.4.excludeName属性

​		通过类的全限制名的方式，排除不扫描的类,指定的类不会在容器中创建对象。

## 	2.4@EnableAutoConfiguration

​	@EnableAutoConfiguration注解的作用是：启动程序时，告诉SpringApplication启动对象使用SpringBoot的默认配置。@EnableAutoConfiguration可以从逐层的往下搜索各个加注解的类，例如，你正在编写一个JPA程序（如果你的pom里进行了配置的话），spring会自动去搜索加了@Entity注解的类，并进行调用

## 	2.5@AutoConfigureBefore注解

​	指定在SpringBoot框架自动配置的配置类执行完成之前，执行指定的自定义的配置类

​	@AutoConfigureBefore注解属性：

​	value:使用类的方式指定自动配置类，@AutoConfigureBefore(value = {SpringConfig.**class**})

​	name：使用类的全限制名（字符串）类指定配置类

## 	2.6 @AutoConfigureAfter注解

​	指定在SpringBoot框架自动配置的配置类执行完成之后，然后执行指定的自定义的配置类。参考                  		@AutConfigureBefore方式	

## 	2.7@SpringBootTest注解和RunWith( )

​	用于使用JUnit测试SpringBoot程序，启动SpringBoot框架。测试SpringBoot一定要加上。

## 2.8@EnableConfigurationProperties

读取自定义配置类

@EnableConfigurationProperties(value={"类名"})

# 3.注解

## 3.1 @Configuration 

​	注解类，将下面的类变成配置类，相当于xml文件的beans

## 3.2 @ControllerAdvice

学习文章地址：https://yq.aliyun.com/articles/647428
作者：[developlee]

```java
package com.developlee.errorhandle.exception;

/**
 * @author Lensen
 * @desc 自定义异常类
 * @since 2018/10/5 11:04
 */
public class CustomException extends RuntimeException {

    private long code;
    private String msg;

    public CustomException(Long code, String msg){
        this.code = code;
        this.msg = msg;
    }

    public long getCode() {
        return code;
    }

    public void setCode(long code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }
}
```

全局异常处理类

```java

/**
 * controller 增强器
 */
 //提供包扫描
@ControllerAdvice（"com.develpoee.errorhandle"） 
public class MyControllerAdvice {

    /**
     * 应用到所有@RequestMapping注解方法，在其执行之前初始化数据绑定器
     * @param binder
     */
    @InitBinder
    public void initBinder(WebDataBinder binder) {}

    /**
     * 把值绑定到Model中，使全局@RequestMapping可以获取到该值
     * @param model
     */
    @ModelAttribute
    public void addAttributes(Model model) {
        model.addAttribute("author", "Magical Sam");
    }

    /**
     * 全局异常捕捉处理
     * @param ex
     * @return
     */
    @ResponseBody
    @ExceptionHandler(value = Exception.class)
    public Map errorHandler(Exception ex) {
        Map map = new HashMap();
        map.put("code", 100);
        map.put("msg", ex.getMessage());
        return map;
    }

}

```

测试

```
/**
 * @author Lensen
 * @desc
 * @since 2018/10/5 11:00
 */
@Controller
public class DemoController {
  
    /**
   * 关于@ModelAttribute,
   * 可以使用ModelMap以及@ModelAttribute()来获取参数值。
   */    
    @GetMapping("/one")
    public String testError(ModelMap modelMap ) {
        throw new CustomException(500L, "系统发生500异常！" + modelMap.get("attribute"));
    }

    @GetMapping("/two")
    public String testTwo(@ModelAttribute("attribute") String attribute) {
        throw new CustomException(500L, "系统发生500异常！" + attribute);
    }
}

//modelMap.get("") 获取model中的值
```

返回

```
{"msg":"系统发生500异常！The Attribute","code":500}
```



# 4.常用api

### 	4.1SpringApplication( 启动类字节码) 

​	用于启动Spring Boot的程序，根据传入的类声明的注解来决定不同的启动方式。一般都在 main方法中调用执行

## 5.SpringBoot执行流程

​	因为spring早已整合了主流框架所以里面内置了很多配置类，默认参数是在properities配置文件，实现硬编码到properities文件下，但是我们依然可以传入我们自己的配置信息，通过.properities来传入自己的信息，

### 	5.1配置流程

​	properities文件下面能找到我们需要的属性前缀。AutoConfiguration会加载默认的.properities类的默认配置

### 	5.2配置文件

​	Spring Boot的参数配置文件支持两种格式。分别为	application.propertie，application.yml。配置Spring Boot时	可以二选一。

​	Spring Boot会加载resources目录下的application.properties来获得配置的参数

#### 		5.2.1 .properities配置文件

		1.各个环境公共的配置写在application.properties中
		2.各个模块独有的配置配置在自己的application-{xxx}.properties文件中
		3.程序读取的时候优先读取application.properties中选中的profile的配置，若读不到才会从application.properties去读
#### 		5.2.2 yml文件

​		springboot带有自制的配置文件

```yaml
#配置数据源
#严格按照前缀来写
#注意：最后key的字段与值之间的冒号（:）后面一定要有一个空格。
spring:
    datasource:
      url: jdbc:mysql://localhost:3306/school
      driverClassName: com.mysql.jdbc.Driver
      username: root
      password: root
      #配置连接池
      type: com.alibaba.druid.pool.DruidDataSource
```

​		yml配置文件也支持多文件配置

```yaml
application-database.yml
application-mvc.yml
application-freemarker.yml
###################################################################################################

2.在application.yml总配置文件指定，加载的多个配置文件
例如：要同时使用，四个配置文件
application.yml
application-database.yml
application-mvc.yml
application-freemarker.yml
###################################################################################################
#那么在application.yml其他配置文件指定为：
spring:
   profiles:
     active: database,mvc,freemarker
```

## 6.配置数据源

### 	6.1在pom.xml文件中导入相应的依赖 sql的依赖，

### 	6.2去配置类查看属性

### 	6.3在配置文件写入配置数据

```
1.spring.datasource这个前缀就是DataSourceProperties的@ConfigurationProperties(prefix = "spring.datasource")注解声明的前缀，属性就是DataSourceProperties对应的属性
------------------------------------------------------------------------------------------
2.@ConfigurationProperties属性说明：
prefix属性：表示获得application.properties时忽略的指定的前缀，如：
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
ignoreUnknownFields属性：忽略未知的字段值。如果为true时，就是当application.properties设置的输入找不到对应的字段时，就忽略它。
```

### 	6.4获取自定义properities声明属性设置

使用@ConfigurationProperties注解可以直接获得application.properties配置的属性值。

```
prefix属性：表示获得application.properties时忽略的指定的前缀，如：
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknownFields = false)
ignoreUnknownFields属性：忽略未知的字段值。如果为true时，就是当application.properties设置的输入找不到对应的字段时，就忽略它。
```

#### 	6.4.1@ConfigurationProperties的使用：

​	1.在pom.xml导入支持的依赖包

```xml
<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<optional>true</optional>
</dependency>	
```

​	2.自定义一个TestProperties 属性类

​	注意：配置类也可以创建对象

## 7.springboot的视图

### 	7.1 springboot不支持jsp

​	想支持jsp就要使用war包

### 	7.2[FreeMarker](http://freemarker.org/docs/)模板引擎的配置

#### 		7.2.1.在pom.xml导入FreeMarker模板引擎依赖的包

```xml
   <!-- freemarker支持包 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

#### 		7.2.2.路径

Spring Boot的模板引擎的默认路径是resources/templates，所以在resources下创建一个templates文件夹。将视图页面放在里面

#### 	     7.2.3可以直接返回ftl视图

#### 	     7.2.4 根据需要修改配置文件

```properties
.application.properties配置文件增加以下配置
#setting freemarker encoding
spring.freemarker.charset=UTF-8
```

### 	7.3 Thymeleaf 模板引擎的配置

#### 		7.3.1配置

```
<!-- thymeleaf支持包 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-thymeleaf</artifactId>
		</dependency>
```

#### 	   7.3.2路径

​	Spring Boot的模板引擎的默认路径是resources/templates，所以在resources下创建一个templates文件夹。将视图页       	面放在里面

#### 		7.3.3.application.properties配置文件

```properties
#增加[Thymeleaf]视图的默认属性。
spring.thymeleaf.mode=HTML5 
spring.thymeleaf.encoding=UTF-8 
spring.thymeleaf.content-type=text/html 
#开发时关闭缓存,不然没法看到实时页面spring.thymeleaf.cache=false
```

## 8.整合mybatis

Spring Boot默认没有对mybatis支持，而是Mybatis对springboot进行了支持。所以Spring Boot整合Mybatis的整合包要去Mybatis的的官方寻找或者第三方Maven仓库

整合完毕之后正常些代码

## 9.打包

### 	9.1 打包部署

SpringBoot支持使用Jar内嵌Web服务器（Tomcat）的方式发布，也支持生成war包放在外置的web服务器运行。

###  	9.2使用jar发布应用

#### 	9.2.1配置步骤

​		1.pom.xml要显示加入插件org.springframework.boot，否则无法产生jar清单文件，导致打出来的jar无法使用命令运行。

```xml
<build>		
    <plugins>			
        <plugin>				
            <groupId>org.springframework.boot</groupId>				
            <artifactId>spring-boot-maven-plugin</artifactId>			
        </plugin>						
        <plugin>				
            <groupId>org.springframework.boot</groupId>				
            <artifactId>spring-boot-maven-plugin</artifactId>                
            <!-- 排除重新打包 -->				
            <executions>					
                <execution>						
                    <goals>							
                        <goal>repackage</goal>						
                    </goals>					
                </execution>				
            </executions>							
        </plugin>			
        <!-- 打包排除单元测试代码 -->			
        <plugin>				
            <groupId>org.apache.maven.plugins</groupId>				
            <artifactId>maven-surefire-plugin</artifactId>				
            <configuration>					
                <skipTests>true</skipTests>				
            </configuration>			
        </plugin>		
    </plugins>			
</build>
```

 

​	2.使用里面package打包

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps1.jpg) 

​	3.打包成功，产生spring-boot-demo-01-1.0.jar文件

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps2.jpg) 

 

​	4.将spring-boot-demo-01-1.0.jar复制到一个文件夹下，在同一级目录编写一个bat文件。

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps3.jpg) 

​	内容格式：#java -jar <jar名>，如下：

​        java -jar spring-boot-demo-01-1.0.jar

 

​	5.双击bat文件startup.bat

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps4.jpg) 

​	6.修改内嵌Tomcat的参数

在application.properties设置相关参数即可，如：

#设置Tomcat端口server.port=80#设置Tomcat路径编码server.tomcat.uri-encoding=UTF-8#设置超时时间server.connection-timeout=1000

 

### 9.3使用war发布应用

1 配置流程

1.修改pom.xml文件去掉嵌入式的Tomcat服务器，以及增加serlvet-api，jstl依赖包

```xml
<!-- jstl -->		<dependency>			
    <groupId>javax.servlet</groupId>			
    <artifactId>jstl</artifactId>		
</dependency>		
<!-- servlet -->		
<dependency>			
    <groupId>javax.servlet</groupId>			
    <artifactId>javax.servlet-api</artifactId>		
</dependency>		
<!-- jsp -->		
<dependency>			
    <groupId>javax.servlet.jsp</groupId>			
    <artifactId>javax.servlet.jsp-api</artifactId>			
    <version>2.2.1</version>		
</dependency>		
<!-- 移除嵌入式tomcat服务器 -->		
<dependency>			
    <groupId>org.springframework.boot</groupId>			
    <artifactId>spring-boot-starter-web</artifactId>						
    <exclusions>				
        <exclusion>					
            <groupId>org.springframework.boot</groupId>					
            <artifactId>spring-boot-starter-tomcat</artifactId>				
        </exclusion>			
    </exclusions>		
</dependency>
```

2.修改pom.xml文件的打包方式为war

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps5.jpg) 

3.增加一个web程序的WEB入口类要和Application同一级目录

```java
package cn.springboot; import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.support.SpringBootServletInitializer;
/* 如果项目需要使用war发布，需要创建这个类，作为web程序的入口*/ 
public class ServletInitializer extends SpringBootServletInitializer { 	
@Override	
protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {	
	//表示获得web的请求时，调用Application类的实现		
return builder.sources(Application.class);	}
}
```

4.打包生成war文件

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps6.jpg) 

5.复制spring-boot-demo-01-1.0.war放在Tomcat的webapps下

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps7.jpg) 

6.启动Tomcat，Tomcat控制台提示SpringBoot信息

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps8.jpg) 

7.浏览器访问，成功

<http://localhost:8080/spring-boot-demo-01-1.0/index>![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml6788\wps9.jpg) 

####  注意事项

####  第1点

web网站入口类一定要继承SpringBootServletInitializer类，而且必须要给SpringBoot入口类Application同一级目录。

#### 第2点

```xml
<?xml version="1.0" encoding="utf-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
  <modelVersion>4.0.0</modelVersion>  
  <parent> 
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>1.5.21.RELEASE</version>  
    <relativePath/>  
    <!-- lookup parent from repository --> 
  </parent>  
  <groupId>cn.zj</groupId>  
  <artifactId>spring-boot-demo01-start</artifactId>  
  <version>0.0.1-SNAPSHOT</version>  
  <packaging>war</packaging>  
  <properties> 
    <java.version>1.8</java.version> 
  </properties>  
  <dependencies> 
    <dependency> 
      <groupId>org.springframework.boot</groupId>  
      <artifactId>spring-boot-starter-web</artifactId>  
      <exclusions> 
        <exclusion> 
          <groupId>org.springframework.boot</groupId>  
          <artifactId>spring-boot-starter-tomcat</artifactId> 
        </exclusion> 
      </exclusions> 
    </dependency>  
    <dependency> 
      <groupId>org.springframework.boot</groupId>  
      <artifactId>spring-boot-starter-test</artifactId>  
      <scope>test</scope> 
    </dependency>  
    <dependency> 
      <groupId>org.springframework.boot</groupId>  
      <artifactId>spring-boot-devtools</artifactId>  
      <!-- optional=true,依赖不会传递，该项目依赖devtools； 之后依赖该项目的项目如果想要使用devtools，需要重新引入 -->  
      <optional>true</optional> 
    </dependency>  
    <!-- spring-boot-jdbc -->  
    <dependency> 
      <groupId>org.springframework.boot</groupId>  
      <artifactId>spring-boot-starter-jdbc</artifactId> 
    </dependency>  
    <!-- druid连接池 -->  
    <dependency> 
      <groupId>com.alibaba</groupId>  
      <artifactId>druid-spring-boot-starter</artifactId>  
      <version>1.1.10</version> 
    </dependency>  
    <!-- mysql驱动 -->  
    <dependency> 
      <groupId>mysql</groupId>  
      <artifactId>mysql-connector-java</artifactId> 
    </dependency>  
    <!-- freemarker 模板 -->  
    <dependency> 
      <groupId>org.springframework.boot</groupId>  
      <artifactId>spring-boot-starter-freemarker</artifactId> 
    </dependency>  
    <!-- mybatis -->  
    <dependency> 
      <groupId>org.mybatis.spring.boot</groupId>  
      <artifactId>mybatis-spring-boot-starter</artifactId>  
      <version>1.3.2</version> 
    </dependency>  
    <!-- jstl -->  
    <dependency> 
      <groupId>javax.servlet</groupId>  
      <artifactId>jstl</artifactId> 
    </dependency>  
    <!-- servlet -->  
    <dependency> 
      <groupId>javax.servlet</groupId>  
      <artifactId>javax.servlet-api</artifactId> 
    </dependency>  
    <!-- jsp -->  
    <dependency> 
      <groupId>javax.servlet.jsp</groupId>  
      <artifactId>javax.servlet.jsp-api</artifactId>  
      <version>2.2.1</version>  
      <scope>provided</scope> 
    </dependency> 
  </dependencies>  
  <build> 
    <plugins> 
      <plugin> 
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-maven-plugin</artifactId> 
      </plugin>  
      <plugin> 
        <groupId>org.apache.maven.plugins</groupId>  
        <artifactId>maven-surefire-plugin</artifactId>  
        <configuration> 
          <!-- 跳过打包安装时自动执行Junit的测试代码 -->  
          <skipTests>true</skipTests> 
        </configuration> 
      </plugin>  
      <!-- 配置编译的时候使用1.8 的JDK -->  
      <plugin> 
        <groupId>org.apache.maven.plugins</groupId>  
        <artifactId>maven-compiler-plugin</artifactId>  
        <configuration> 
          <!-- 源码检查时使用1.8的JDK -->  
          <source>1.8</source>  
          <!-- 源码打包成目标程序时，使用1.8JDK -->  
          <target>1.8</target> 
        </configuration> 
      </plugin>  
      <!-- 忽略项目的 web.xml -->  
      <plugin> 
        <groupId>org.apache.maven.plugins</groupId>  
        <artifactId>maven-war-plugin</artifactId>  
        <configuration> 
          <failOnMissingWebXml>false</failOnMissingWebXml> 
        </configuration> 
      </plugin> 
    </plugins> 
  </build>
</project>

```



#### 第3点：将映射接口使用@Mapper

@Mapper**public** **interface** ModularMapper 	

 

#### 第4点，关闭thymeleaf模板引擎

注意，关闭thymeleaf模板引擎 

忽略默认的模板引擎spring.thymeleaf.cache=falsespring.thymeleaf.enabled=false 

####  第5点,配置SpringMVC的视图前后缀

配置springMVC的前后缀spring.mvc.view.prefix=/WEB-INF/view/spring.mvc.view.suffix=.jsp

####  第6点，启动的是WebApplicationInitializer类

所以直接启动页面就可以

```java
package cn.zj.springboot.platform;

import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.support.SpringBootServletInitializer;


public class WebApplicationInitializer extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(
        SpringApplicationBuilder builder) {
        return builder.sources(Application.class);
    }
}

```

