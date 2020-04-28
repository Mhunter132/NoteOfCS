# IOC注解开发

### spring整合连接池

spring不仅可以实例化自己写的类也可以实现第三方的类。例如c3p0硬编码连接池。

```java
public void test1() throws Exception  //c3p0
	{
		ComboPooledDataSource dataSource=new ComboPooledDataSource(); //IOC
		
		// 设置驱动
		dataSource.setDriverClass("com.mysql.jdbc.Driver");
		// 设置地址
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/hibernate");
		// 设置用户名
		dataSource.setUser("root");
		// 设置密码
		dataSource.setPassword("1234");	//DI
		
		// 问连接池要连接对象
		Connection con = dataSource.getConnection();
		System.out.println(con);
	}
```

既然是创建对象并初始化，那么将这个过程交给spring管理，用context的名称空间和约束条件可以加载properties文件。

```properties
//properties文件
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/hibernate
jdbc.username=root
jdbc.password=1234


#jdbc.driver=com.mysql.jdbc.Driver
#jdbc.url=jdbc:oracle://localhost:3306/hibernate
#jdbc.username=root
#jdbc.password=1234


#jdbc.driver=com.mysql.jdbc.Driver
#jdbc.url=jdbc:db2://localhost:3306/hibernate
#jdbc.username=root
#jdbc.password=1234
```

properties文件用来切换数据库，导入expression包就可以使用cgnil表达式

```xml
<!--xml装配连接池-->
!-- 让spring能够加载jdbc.properties文件
		 spring提供了一个标签 可以加载外部的properties文件内容
		 导context的名称空间和约束 才会有提示
	-->
<context:property-placeholder location="classpath:jdbc.properties"/>
---------------------------------------------------------------------------------------------------
<bean id="c3p0" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${jdbc.driver}"></property>
		<property name="jdbcUrl" value="${jdbc.url}"></property>
		<property name="user" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
</bean>
---------------------------------------------------------------------------------------------------
<!-- 创建dao -->
<bean id="userDao" class="cn.itcast.daoimpl.UserDaoImpl"></bean>
---------------------------------------------------------------------------------------------------
<!-- 创建service -->
<bean id="userService"  class="cn.itcast.serviceimpl.UserServiceImpl">
    	<property name="name" value="要开始访问dao了"></property>
    	<property name="userDao" ref="userDao"></property>
</bean>
```

### spring的IOC注解

帮助解半xml配置方式

​	自己的类全部是注解

​	别人的类全部都是xml

1 导包 spring-aop.jar

2 配置注解扫描器
	<context:component-scan base-package="cn.itcast"></context:component-scan >

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" 
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd"> 
<!--xml装配连接池-->
!-- 让spring能够加载jdbc.properties文件
		 spring提供了一个标签 可以加载外部的properties文件内容
		 导context的名称空间和约束 才会有提示
	-->
<context:property-placeholder location="classpath:jdbc.properties"/>
---------------------------------------------------------------------------------------------------
<bean id="c3p0" class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<property name="driverClass" value="${jdbc.driver}"></property>
		<property name="jdbcUrl" value="${jdbc.url}"></property>
		<property name="user" value="${jdbc.username}"></property>
		<property name="password" value="${jdbc.password}"></property>
</bean>
---------------------------------------------------------------------------------------------------
<!-- 创建dao -->
<bean id="userDao" class="cn.itcast.daoimpl.UserDaoImpl"></bean>
---------------------------------------------------------------------------------------------------
<!-- 创建service -->
<bean id="userService"  class="cn.itcast.serviceimpl.UserServiceImpl">
    	<property name="name" value="要开始访问dao了"></property>
    	<property name="userDao" ref="userDao"></property>
</bean>
---------------------------------------------------
<!--扫描注解-->
<context:component-scan base-package="cn.sxt.service.impl"></context:component-scan>

```

在类上面打注解@service（"service"）相当于在xml文件中<bean id="service">.但是如果进行类初始化，就需要用到@value（"内容"） 这个注解只能给基本类型诸如。**set（）方法可以省略不写**如果是引用类型就是用@Autowired ，这个注解spring会自动完成类型的匹配。

```java
@scope("prototype") //多例
@Service("userService") <!--在impl包下面的类上面添加@component("自命名的id") 相当<bean id="" class="">  -->
public class UserServiceImpl implements UserService
{
	@Value("要开始访问dao了")
	private String name;
	
	//@Autowired // 自动去spring容器中找有没有该类型(UserDao)的实例对象 如果有直接赋值
	//@Qualifier("userDaoxxx") // 指定用该类型的哪个名称的实例对象
	@Resource（name="id"）//写括号就是名称指定，不写就是类型指定
	private UserDao userDao;

	public void setUserDao(UserDao userDao) {
		this.userDao = userDao;
	}
	@Override
	public void save() 
	{
		System.out.println(name);
		// 调用dao
		userDao.save();
		
	}
-------------------对象初始化方法，类销毁执行的方法------------------------------------------------	
	// 初始化方法
	@PostConstruct
	public void init()
	{
		System.out.println("userService的初始化方法....");
	}
	
	// 销毁的方法
	@PreDestroy
	public void destory()
	{
		System.out.println("userService的销毁方法....");
	｝
}

```

@component("bean的ID") 只要注解扫描器扫描到注解就会在spring中创建对象。

### 新注解/全注解开发

条件：需要有一个注解类，不再加载配置文件，而是加载这个注解类。

```java
@Configuration // 表示该类是一个注解类
@ComponentScan(basePackages="cn.itcast")  //<context:component-scan base-package="cn.itcast"></context:component-scan>
@PropertySource(value="classpath:jdbc.properties")//<context:property-placeholder location="classpath:jdbc.properties"/>
@Import(value = {DataSourceconfig.class})
public class SpringConfig
{
	@Value("${jdbc.driver}")
	private String driver;
	@Value("${jdbc.url}")
	private String url;
	@Value("${jdbc.username}")
	private String username;
	@Value("${jdbc.password}")
	private String password;
	
	// 自己创建c3p0连接池 给spring容器
	@Bean(name="c3p0")  //<bean id="c3p0" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	public DataSource createDataSourceC3p0() throws Exception
	{
		ComboPooledDataSource ds = new ComboPooledDataSource();
		ds.setDriverClass(driver);
		ds.setJdbcUrl(url);
		ds.setUser(username);
		ds.setPassword(password);
		return ds;
	}
	
	// 4.3以前 要给spring配置一个解析器 来解析 ${}
	@Bean
	public static PropertySourcesPlaceholderConfigurer createPropertySourcesPlaceholderConfigurer()
	{
		return new PropertySourcesPlaceholderConfigurer();
	}
```

或者在另外一个文件创建配置类然后导进另一个类里面。@value()给字段注入值，@bean

```java

public class DataSourceconfig 
{
	@Value("${jdbc.driver}")
	private String driver;
	@Value("${jdbc.url}")
	private String url;
	@Value("${jdbc.username}")
	private String username;
	@Value("${jdbc.password}")
	private String password;
	
	// 自己创建c3p0连接池 给spring容器
	@Bean(name="c3p0")  //<bean id="c3p0" class="com.mchange.v2.c3p0.ComboPooledDataSource">  //返回的是一个引用类型
	public DataSource createDataSourceC3p0() throws Exception
	{
		ComboPooledDataSource ds = new ComboPooledDataSource();
		ds.setDriverClass(driver);
		ds.setJdbcUrl(url);
		ds.setUser(username);
		ds.setPassword(password);
		return ds; 
	}
	
	// 4.3以前 要给spring配置一个解析器 来解析 ${}
	@Bean
	public static PropertySourcesPlaceholderConfigurer createPropertySourcesPlaceholderConfigurer()
	{
		return new PropertySourcesPlaceholderConfigurer();
	}
}
---------------------------------------------------------------------------------------------------
    public class SpringConfigTest 
  {
	@Test
	public void test() throws SQLException
	{
	// 加载注解类
	   ApplicationContext context=new AnnotationConfigApplicationContext(SpringConfig.class);
	 /* UserService userService=(UserService)context.getBean("userService");
	   userService.save();*/
	   DataSource ds =(DataSource)context.getBean("c3p0");
	   Connection con = ds.getConnection();
	   System.out.println(con);
	}
}

```

### spring整合junit测试

1 导包
					spring-test.jar 增强
					spring-aop.jar 可以写注解
					junit.jar  还是之前的测试

2 要告诉spring谁加载配置文件(SpringJunt4ClassRunner.class)

3 要告诉spring配置文件的地址

4 分层测试

```java
/*1:告诉spring配置文件在哪个地方*/
@ContextConfiguration(value="classpath:applicationContext.xml")
/*2:告诉spring谁加载配置文件*/
@RunWith(value =SpringJUnit4ClassRunner.class)
public class SpringJunit 
{
	@Autowired
	private UserService userSerive;
	@Autowired
	private UserDao userDao;
	
	@Test
	public void test1()
	{
		userDao.save();
	}
}

```

