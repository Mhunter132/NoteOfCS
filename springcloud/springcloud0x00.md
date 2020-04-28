# 1.springcloud

## 1.1从面试题开始

什么是微服务？

答：将单一程序（一个应用程序）分解成很多小的服务，每个服务有独立的进程，进程互相配合，分布式采用rpc通信，微服务是采用轻量级的通信机制（Restful API）。

微服务之间是如何通讯的？

答：RESTful API

springcloud和dubbo有哪些区别，有哪些优缺点？

答：

springboot和springcloud，请你谈谈对他们的理解。

答：关注开发是单个应用程序（服务）

springcloud是关注的微服务协调整理治理框架，它将springboot开的单个微服务整合管理，springboot可以离开springcloud，但是反之不行。

什么是服务熔断，什么是服务降级。

答：

微服务的优缺点，请你说说你的项目碰到过什么坑。

答：

优点：每个服务足够内聚，足够小，代码容易理解，开发简单，微服务被小团队开发，易于与第三方集成，微服务只是业务逻辑代码，不会喝html和css，或者其他的界面组件混合，可以灵活搭配公共库或者独立库。 

缺点：分布式系统变得很复杂，运维难度上升，运维压力大，服务与服务之间的依赖，服务间的通信成本增加，数据一致性难保证，系统集成测试增加。

你所知道的微服务技术栈有哪些？请列举一二。

答：是多种技术的集合体，分布式微服务架构的维度。

- 服务开发
- 服务管理和配置，
- 服务注册，

- 服务调用，

- 服务负载均衡，

- 服务面监控，

- 服务路由（API网关）

- 服务配置中心管理

- 消息队列

- 服务接口调用、

- 服务部署

- 数据流操作开发包.

- 时间消息总线

springcloud提供一条龙服务

eurka和zookeeper都可以提供服务注册与发现的功能，请说说区别。

答：

## 1.2微服务概述

微服务是什么？

答：将单一程序（一个应用程序）分解成很多小的服务，每个服务有独立的进程，互相配合，分布式是采用rpc通信，微服务是采用轻量级的通信机制（Restful API）。

微服务与微服务架构

答：

微服务优缺点。

答：

你所知道的微服务技术栈有哪些？请列举一二。

答：

为什么选择springcloud作为微服务架构

答：选型依据

分布式：专业的事情专业的人来做，尽量降低耦合度，

## 1.3springCloud入门概述

![1565924990446](D:\Documents\Markdown文档\springcloud\1565924990446.png)

基于springboot提供了一套的微服务解决方案，

## 1.4Rest微服务的构建案例工程模块

### 1.4.1.父工程

的package一定是pom模式，主要是定义pom文件，将后续的子工程的公用的pom文件统一提出来，类似于做一个抽象	

![1565936285231](D:\Documents\Markdown文档\springcloud\1565936285231.png)

保证编译时java版本

```xml
<parent>******</parent>
<modules>
<module>子工程</module>
</modules>
```



### 1.4.2.子工程-api

作为api给其他模块使用

当前module需要用到的jar包，按照需要包含，lombok如果父类版本锁定已经包含了，可以不用写版本号。

```xml
<version>${EL表达式project.version}</version>
```

lombok

@NoArgsConstrusctor   无参数构造器

@AllArgsConstructor 全参数构造器

@Data   getter  setter 方法

@Accessors (chain=true)  链式调用

mvn clean install 后给其他模块使用，达到通用的目的。也及需要用到部门实体的话，不用每个工程都定义一份，直接引用模块。

### 1.4.3.子工程-provider-dept-8001作为微服务提供者

引用api工程要在pom文件里面导入依赖

```xml
project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
-----------------------------------------------------------------------------------------
	<parent>
		<groupId>com.atguigu.springcloud</groupId>
		<artifactId>microservicecloud</artifactId>
		<version>0.0.1-SNAPSHOT</version>
	</parent>
-----------------------------------------------------------------------------------------
	<artifactId>microservicecloud-provider-dept-8001</artifactId>
-----------------------------------------------------------------------------------------
	<dependencies>
-----------------------------------------------------------------------------------------
		<!-- 引入自己定义的api通用包，可以使用Dept部门Entity -->
		<dependency>
			<groupId>com.atguigu.springcloud</groupId>
			<artifactId>microservicecloud-api</artifactId>
			<version>${project.version}</version>
		</dependency>
-----------------------------------------------------------------------------------------
		<!-- actuator监控信息完善 -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<!-- 将微服务provider侧注册进eureka -->
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-core</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jetty</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
		<!-- 修改后立即生效，热部署 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>springloaded</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
		</dependency>
	</dependencies>

</project>
```



1. 新建maven子模块provider-dept-8001
2. pom文件。依赖父工程，引用版本${project.version}
3. yml配置文件-待续
4. mybatis.cfg.xml   最后会配置到spring配置文件中
5. 创建sql数据库脚本
6. springcloud-DAO,创建DepDao接口别忘了mybatis的@mapper注解，mapper文件内容要与配置文件路径相同
7. 创建service接口包和实现包
8. 控制器constroller包-@RestConstroller 天然返回json串。

```
@RequestBody 防在参数前
@RequestMapping (value="/dept/add" method=RequestMethod.GET)
@RequestMapping (value="/dept/get/{id}" method=RequestMethod.GET)
public dept get(@PathVariable{"id"} Long id){}
```
```
@requestBody注解的使用
	1、@requestBody注解常用来处理content-type不是默认的application/x-www-form-urlcoded编码的内容，比如说：application/json或者是application/xml等。一般情况下来说常用其来处理application/json类型。

　　2、通过@requestBody可以将请求体中的JSON字符串绑定到相应的bean上，当然，也可以将其分别绑定到对应的字符串上。
　　　　例如说以下情况：
　　　　$.ajax({
　　　　　　　　url:"/login",
　　　　　　　　type:"POST",
　　　　　　　　data:'{"userName":"admin","pwd","admin123"}',
　　　　　　　　content-type:"application/json charset=utf-8",
　　　　　　　　success:function(data){
　　　　　　　　　　alert("request success ! ");
　　　　　　　　}
　　　　});

　　　　@requestMapping("/login")
　　　　public void login(@requestBody String userName,@requestBody String pwd){
　　　　　　System.out.println(userName+" ："+pwd);
　　　　}
　　　　这种情况是将JSON字符串中的两个变量的值分别赋予了两个字符串，但是呢假如我有一个User类，拥有如下字段：
　　　　　　String userName;
　　　　　　String pwd;
　　　　那么上述参数可以改为以下形式：@requestBody User user 这种形式会将JSON字符串中的值赋予user中对应的属性上
　　　　需要注意的是，JSON字符串中的key必须对应user中的属性名，否则是请求不过去的。

　 3、在一些特殊情况@requestBody也可以用来处理content-type类型为application/x-www-form-urlcoded的内容，只不过这种方式
	  不是很常用，在处理这类请求的时候，@requestBody会将处理结果放到一个MultiValueMap<String,String>中，这种情况一般在
　　　　特殊情况下才会使用，
　　　　例如jQuery easyUI的datagrid请求数据的时候需要使用到这种方式、小型项目只创建一个POJO类的话也可以使用这种接受方式




```
9. 主启动类

```
@SpringBootApplication
public class DeptProvider8001_App{
public static void main(String[] args){

   SpringApplication.run(DeptProvider8001_App.class)
 }
}

```

## 1.5.Eureka自我保护

可以在application.yml文件里面配置 info：instance 来完善服务信息。包括更改名字，显示ip地址。

Eureka有自我保护机制不会马上DOWN掉服务

可以通过服务发现能快速查看服务信息，在服务提供方的@Constroller中添加接口@DiscoverstryClient，在服务提供方上面添加@EnableDiscoverstryClient

## 1.6.Eureka集群搭建

127.0.0.1Eureka7001

127.0.0.1Eureka7003

127.0.0.1Eureka7002-两个工程-这两个工程是server是注册中心。

域名区分和映射，找hosts文件，改映射。

然后去Server-url改defaultZone: 写另外几个host地址然后用逗号隔开。

-->然后去服务的提供者，client的server-url的defaultzone上所有的hosts地址。

## 1.7.Eureka和zookeeper的区别

Eureka遵守ap原则

zookeeper遵守cp原则

传统数据库是acid

cap: 
｛
Consistency:强一致性，
Availability:可用性，
Partition tolerance:分区容错性 
}
所有分布式系统只能满足其中的两个



# 2.Ribbon

客户端的负载均衡工具，springcloud可以自定义负载均衡算法

-集中式：在服务方和消费方架设

-进程式：放在消费方



### 2.1.Ribbon配置步骤

1. 建工程
2. pom添加依赖 ribbon和eureka整合，没有server说明是客户端。
3. @LoadBalanced 在消费者RestTemplate方法配置该 注解
4. 主启动类上添加@EnableEurekaClient
5. 修改客户端的访问类
6. 访问类改服务名字（按名字访问）
7. 再多建工程然后改端口改pom文件，

Ribbon在工作时分为两步：{出场提供了其中算法：

```java
ribbon为我们封装了7种负载均衡算法：

　　1.BestAvailableRule：选择一个最小的并发请求的server
　　2.AvailabilityFilteringRule：过滤掉那些因为一直连接失败的被标记为circuit tripped的后端server，并过滤掉那些高并发的的后端server（active connections 超过配置的阈值）
　　3.WeightedResponseTimeRule：根据相应时间分配一个weight，相应时间越长，weight越小，被选中的可能性越低。
　　4.RetryRule：对选定的负载均衡策略机上重试机制。（RoundRobinRule的加强版，在轮训时发现某些服务无法连接，如果这些服务超过了设定的连接数，之后就会跳过这些服务进行轮训）
　　5.RoundRobinRule：轮训（ribbon默认的算法）
　　6.RandomRule：随机选中一个server
　　7.ZoneAvoidanceRule：复合判断server所在区域的性能和server的可用性选择server


 如何选择ribbon自带的算法：

　　 我们只需要指定IRule实现就可以，实现的类就是上面介绍7种算法的名字。

　　 在服务调用端中，RibbonConfig类文件下我们添加IRule的实现代码
@Bean
public IRule ribbonRule() {
    return new RetryRule();//RetryRule就是我们上面介绍的7种算法，默认的实现是RoundRobinRule算法
}
```



1. 先选择EurekaServer，选择负载均衡较少的server

2. 在根据用户指定的策略，然后在服务器上选择一个ribbon，比如轮询或者随机

   

### 2.2.自定义Ribbon算法

主启动类上添加@RibbonClient（name="f服务提供者的名字"，configuration=MySelfRule.class），自定义负载均衡算法不能在放在启动类下  

并将自定义算法注入到ioc容器内









































#  问题

## 1. setting.xml文件要设置插件地址

```xml
<profiles>
     <profile>
      <id>spring plugins</id>

      <activation>
        <jdk>spring plugins</jdk>
      </activation>

      <pluginRepositories>
        <pluginRepository>
          <id>spring plugins</id>
          <name>Spring plugins</name>
          <url>https://maven.aliyun.com/repository/spring-plugin</url>
          <layout>default</layout>
          <snapshotPolicy>always</snapshotPolicy>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
<!---- -------------配置springboot插件-------------- -->
<build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <maimClass>com.hhly.InformationApplication</maimClass>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>

            </plugin>
        </plugins>
    </build>
```

## 2自定义异常

```java

class ChushulingException extends Exception
{
	public ChushulingException(String msg)
	{
		super(msg);
	}
} 
 
class ChushufuException extends Exception
{
	public ChushufuException(String msg)
	{
		super(msg);
	}
}
 
/*自定义异常 End*/
 
class Numbertest 
{
	public int shang(int x,int y) throws ChushulingException,ChushufuException
	{
		if(y<0)
		{
			throw new ChushufuException("您输入的是"+y+",规定除数不能为负数!");//抛出异常
		}
		if(y==0)
		{
			throw new ChushulingException("您输入的是"+y+",除数不能为0!");
		}
	
		int m=x/y;
		return m;
	}
}

```

## 3.springboot出现datasource问题

```
Error starting ApplicationContext. To display the auto-configuration report re-run your application with 'debug' enabled.
2017-05-06 22:44:18.907 ERROR 41648 --- [ restartedMain] o.s.b.d.LoggingFailureAnalysisReporter :

***************************
APPLICATION FAILED TO START
***************************

Description:

Cannot determine embedded database driver class for database type NONE

Action:

If you want an embedded database please put a supported one on the classpath. If you have database settings to be loaded from a particular profile you may need to active it (no profiles are currently active).

这是因为spring boot默认会加载org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration类，DataSourceAutoConfiguration类使用了@Configuration注解向spring注入了dataSource bean。因为工程中没有关于dataSource相关的配置信息，当spring创建dataSource bean因缺少相关的信息就会报错。

 

因为我仅仅只是使用spring boot来写一些很简单的例子来学习它，在Application类上增加@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})

阻止spring boot自动注入dataSource bean
```

```
 <!--<dependency>-->
                <!--<groupId>tk.mybatis</groupId>-->
                <!--<artifactId>mapper-spring-boot-starter</artifactId>-->
                <!--<version>2.1.5</version>-->
            <!--</dependency>-->
            <!--<dependency>-->
                <!--<groupId>tk.mybatis</groupId>-->
                <!--<artifactId>mapper-spring-boot-starter</artifactId>-->
                <!--<version>1.2.3</version>-->
            <!--</dependency>-->
                <!--<exclusions>-->
                <!--<exclusion>-->
                    <!--<groupId>org.springframework.boot</groupId>-->
                    <!--<artifactId>spring-boot-starter-jdbc</artifactId>-->
                <!--</exclusion>-->
            <!--</exclusions>-->


            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>2.0.1</version>
            </dependency>
            <!--<dependency>-->
                        <!--<groupId>org.mybatis.spring.boot</groupId>-->
                        <!--<artifactId>mybatis-spring-boot-starter</artifactId>-->
                        <!--<version>2.0.1</version>-->
                        <!--<exclusions>-->
                            <!--<exclusion>-->
                                <!--<groupId>org.mybatis</groupId>-->
                                <!--<artifactId>mybatis</artifactId>-->
                            <!--</exclusion>-->
                            <!--<exclusion>-->
                                <!--<groupId>org.mybatis</groupId>-->
                                <!--<artifactId>mybatis-spring</artifactId>-->
                            <!--</exclusion>-->
                        <!--</exclusions>-->
             <!--</dependency>-->
```

