# icebreaker_of_mybatis

### mybatis的架构

​	1、核心的配置文件 sqlMapConfig.xml      
​	2、映射文件       XXXMapper.xml                      
​	3、sqlSessionFactory----->sqlSession-->执行器（executor）--> mapperStatement(封装的是sql语句)
​	4、输入映射（传入的参数）    支持的数据类型：
​    	   基本数据类型：基础类型以及包装类、String，POJO，Map
​    	   包装的POJO：一个pojo中有pojo属性
​	5、输出映射（产生的结果类型）
​      	  基本数据类型：基础类型以及包装类、String，POJO，Map，List。

复习一下jdbc

```java
public class JDBCTest {

	public static void main(String[] args) {
//		需求：根据id查询用户  select * from user where id=10
		Connection connection = null;
		PreparedStatement stmt = null;
		ResultSet rs = null;
		try {
//			1、加载驱动
			Class.forName("com.mysql.jdbc.Driver");
//			2、获取连接（对象）
			connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis", "root", "root");
//			3、获取预处理
			stmt = connection.prepareStatement("select * from user where id= ?");
//			4、设置参数
			stmt.setInt(1, 10);
//			5、执行,得到了结果集
			rs = stmt.executeQuery();
//			6、从结果集中获取结果
			while(rs.next()){
				
				System.out.println(rs.getInt(1)+"--"+rs.getString(2));
//				rs.getInt("id");
			}
		} catch (Exception e) {
			e.printStackTrace();
		}finally {
			try {
				rs.close();//close结果集
				stmt.close();//关掉预处理
				connection.close();//关掉连接对象
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}
```

### mybatis可以解决问题

​	1、 数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。

解决：在SqlMapConfig.xml中配置数据链接池，使用连接池管理数据库链接。

​	2、 Sql语句写在代码中造成代码不易维护，实际应用sql变化的可能较大，sql变动需要改变java代码。

解决：将Sql语句配置在XXXXmapper.xml文件中与java代码分离。

​	3、 向sql语句传参数麻烦，因为sql语句的where条件不一定，可能多也可能少，占位符需要和参数一一对应。

解决：Mybatis自动将java对象映射至sql语句，通过statement中的parameterType定义输入参数的类型。

​	4、 对结果集解析麻烦，sql变化导致解析代码变化，且解析前需要遍历，如果能将数据库记录封装成pojo对象解析比较方便。

解决：Mybatis自动将sql执行结果映射至java对象，通过statement中的resultType定义输出结果的类型。

 

###  SqlMapConfig.xml配置文件

我通常叫这个文件为主配置文件，且严格按照这个顺序书写。我们先弄明白标签名字再去写代码。

​	**properties**（属性）

​	**settings**（全局配置参数）

​	**typeAliases**（类型别名）

​	**typeHandlers**（类型处理器）

​	**objectFactory**（对象工厂）

​	**plugins**（插件）

​	**environments**（环境集合属性对象）

​	**environment**（环境子属性对象）

​	**transactionManager**（事务管理）

​	**dataSource**（数据源）

​	**mappers**（映射器）

####  properties（属性）

​	xml主配置文件可以加载properties文件。

​	

```properties
//这种文件可以灵活的加载配置信息。文件名：db.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
```

SqlMapConfig.xml引用如下：

 

```xml
<!--
我们一般会设置两个environment一个用户开发环境，使用id为development，environments的default属性选择使用对应的environment了
JDBC – 声明使用jdbc方式来提交和回滚事务，依赖于从数据源得到的连接来管理事务范围。
POOLED – 声明使用数据库连接池，从而避免频繁的创建和销毁链接造成资源的浪费
-->
<properties resource="db.properties"/>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC"/>
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}"/>
				<property name="url" value="${jdbc.url}"/>
				<property name="username" value="${jdbc.username}"/>
				<property name="password" value="${jdbc.password}"/>
			</dataSource>
		</environment>
	</environments>
<!--
或者使用这种方式配置
-->
<properties resource= "jdbc . properties"> <!--resource= 从外部引入文件，并自定义值。不在src下就要写全路径，-->
	<property name="jdbc. driver" value="com. mysql . jdbc . Driver"/>
	<property name="jdbc. url" value="jdbc ：mysql://localhost：3306/mybatis？characterEncoding=utf-8"/>
	<property name="jdbc . username" voluc- "rcot"/>
	<property name= "jdbc. password" value="root"/>
</properties>
<properties resource="db.properties"/>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC"/>
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}"/>
				<property name="url" value="${jdbc.url}"/>
				<property name="username" value="${jdbc.username}"/>
				<property name="password" value="${jdbc.password}"/>
			</dataSource>
		</environment>
	</environments>
```

#### 自定义别名：

```xml
<typeAliases>
	<!-- 单个别名定义 -->
	<typeAlias alias="user" type="cn.mybatis.po.User"/>
	<!-- 批量别名定义，扫描整个包下的类，别名为类名（首字母大写或小写都可以） -->
	<package name="cn.itcast.mybatis.po"/>
	<package name="其它包"/>
</typeAliases>
```

| 别名       | 映射的类型 |
| ---------- | ---------- |
| _byte      | byte       |
| _long      | long       |
| _short     | short      |
| _int       | int        |
| _integer   | int        |
| _double    | double     |
| _float     | float      |
| _boolean   | boolean    |
| string     | String     |
| byte       | Byte       |
| long       | Long       |
| short      | Short      |
| int        | Integer    |
| integer    | Integer    |
| double     | Double     |
| float      | Float      |
| boolean    | Boolean    |
| date       | Date       |
| decimal    | BigDecimal |
| bigdecimal | BigDecimal |
| map        | Map        |

上面表格是mybatis完成的别名。

####  mappers（映射器）

```xml
<!-- 加载映射文件 -->
<mappers>
	<mapper resource="sqlmap/User.xml" /> <!--使用工程路径下的接口配置文件-->
	<mapper class="cn.mybatis.mapper.UserMapper " /><!--此种方法要求mapper接口名称和mapper映射文件名	称相同，且放在同一个目录中-->
	<package name="cn.mybatis.mapper"/><!--注册指定包下的所有mapper接口,此种方法要求mapper接口名称和		mapper映射文件名称相同，且放在同一个目录中-->
</mappers>
```

#### Mybatis工程构建步骤

##### 	第一步：导包

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml8348\wps1.jpg)

##### 第二步：log4j.properties

​		

```properties
 Global logging configuration
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

##### 第三步：构建主配置文件

```xml
//根据开发规范命名
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 和spring整合后 environments配置将废除-->
	<environments default="development1">
		<environment id="development1">
		<!-- 使用jdbc事务管理-->
			<transactionManager type="JDBC" />
		<!-- 数据库连接池-->
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8" />
				<property name="username" value="root" />
				<property name="password" value="root" />
			</dataSource>
		</environment>
	</environments>
	<mappers>

    </mappers>
</configuration>
```

##### 第四步：pojo类

##### 第五步：写mapper.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="test">
</mapper>

namespace ：命名空间，用于隔离sql语句，后面会讲另一层非常重要的作用。
```

##### 第六步：加载mapper.xml文件

```xml
<mappers>
		<mapper resource="sqlmap/User.xml"/>
</mappers>
```

#####  #{}和${}

​	#{}表示一个占位符号，通过#{}可以实现preparedStatement向占位符中设置值，自动进行java类型和jdbc类型转换，#{}可以有效防止sql注入。 #{}可以接收简单类型值或pojo属性值。 如果parameterType传输单个**简单类型**值，**#{}括号中可以是value或其它名称.**

​	${}表示拼接sql串，通过${}可以将parameterType 传入的内容拼接在sql中且不进行jdbc类型转换， ${}可以接收**简单类型**值或**pojo属性值**，如果parameterType传输单个简单类型值，**${}括号中只能是value。**

##### parameterType和resultType

​	parameterType：指定输入参数类型，mybatis通过ognl从输入对象中获取参数值拼接在sql中。

​	resultType：指定输出结果类型，mybatis将sql查询结果的一行记录数据映射为resultType指定类型的对象。

### SqlSession的使用范围

​	SqlSession中封装了对数据库的操作，如：查询、插入、更新、删除等。

通过SqlSessionFactory创建SqlSession，而SqlSessionFactory是通过SqlSessionFactoryBuilder进行创建。

 

#### SqlSessionFactoryBuilder

SqlSessionFactoryBuilder用于创建SqlSessionFacoty，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

 

#### SqlSessionFactory

​	SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。

 

#### SqlSession

​	SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作方法。

​	每个线程都应该有它自己的SqlSession实例。SqlSession的实例不能共享使用，它也是线程不安全的。因此最佳的范围是请求或方法范围。绝对不能将SqlSession实例的引用放在一个类的静态字段或实例字段中。

​	打开一个 SqlSession；使用完毕就要关闭它。通常把这个关闭操作放到 finally 块中以确保每次都能执行关闭。如下：

​	

```java
SqlSession session = sqlSessionFactory.openSession();

try {
 		// do work
} finally {

  		session.close();
}
```

### mapper代理对象编写dao层

​	**selectOne和selectList**

动态代理对象调用sqlSession.selectOne()和sqlSession.selectList()是根据mapper接口方法的返回值决定，如果返回list则调用selectList方法，如果返回单个对象则调用selectOne方法。

 	**namespace**

mybatis官方推荐使用mapper代理方法开发mapper接口，程序员不用编写mapper接口实现类，使用mapper代理方法时，输入参数可以使用pojo包装对象或map对象，保证dao的通用性。

​	**Mapper接口开发需要遵循以下规范：**

1、接口的全路径和mapper文件中的namespace保持一致

2、接口的方法名和映射文件的statementId保持一致

3、接口中方法的参数类型和映射文件的parameterType保持一致，返回结果类型和映射文件的resultType保持一致

4、接口名称和映射文件的名称最好保持一致

5、接口和映射文件最好放到一起

#### mapper映射文件

​	定义mapper映射文件UserMapper.xml（内容同Users.xml），需要修改namespace的值为 UserMapper接口路径。将UserMapper.xml放在classpath 下mapper目录 下。

```xml

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.itcast.mybatis.mapper.UserMapper">
<!-- 根据id获取用户信息 -->
	<select id="findUserById" parameterType="int" resultType="cn.itcast.mybatis.po.User">
		select * from user where id = #{id}
	</select>
<!-- 自定义条件查询用户列表 -->
	<select id="findUserByUsername" parameterType="java.lang.String" 
			resultType="cn.itcast.mybatis.po.User">
	   select * from user where username like '%${value}%' 
	</select>
<!-- 添加用户 -->
	<insert id="insertUser" parameterType="cn.itcast.mybatis.po.User">
	<selectKey keyProperty="id" order="AFTER" resultType="java.lang.Integer"> 
		select LAST_INSERT_ID()   <！--注意这个AFTER关键字，它是--> 
	</selectKey>
	  insert into user(username,birthday,sex,address) 
	  values(#{username},#{birthday},#{sex},#{address})
	</insert>
</mapper>
```



#### UserMapper.java文件

```java
/**
 * 用户管理mapper
 */
Public interface UserMapper {
	//根据用户id查询用户信息
	public User findUserById(int id) throws Exception;
	//查询用户列表
	public List<User> findUserByUsername(String username) throws Exception;
	//添加用户信息
	public void insertUser(User user)throws Exception; 
}
```

#### 按需求编写pojo

#### 加载UserMapper.xml文件

```xml


  <!-- 加载映射文件 -->
  <mappers>
    <mapper resource="mapper/UserMapper.xml"/>
  </mappers>
```

#### 测试

```java
Public class UserMapperTest extends TestCase {


	private SqlSessionFactory sqlSessionFactory;
	@Before 
	public void setUp() throws Exception {
		//mybatis配置文件
		String resource = "sqlMapConfig.xml";
		InputStream inputStream = Resources.getResourceAsStream(resource);
		//使用SqlSessionFactoryBuilder创建sessionFactory
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}

	
	Public void testFindUserById() throws Exception {
		//获取session
		SqlSession session = sqlSessionFactory.openSession();
		//获取mapper接口的代理对象
		UserMapper userMapper = session.getMapper(UserMapper.class);
		//调用代理对象方法
		User user = userMapper.findUserById(1);
		System.out.println(user);
		//关闭session
		session.close();
		
	}
	@Test
	public void testFindUserByUsername() throws Exception {
		SqlSession sqlSession = sqlSessionFactory.openSession();
		UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
		List<User> list = userMapper.findUserByUsername("张");
		System.out.println(list.size());

	}
Public void testInsertUser() throws Exception {
		//获取session
		SqlSession session = sqlSessionFactory.openSession();
		//获取mapper接口的代理对象
		UserMapper userMapper = session.getMapper(UserMapper.class);
		//要添加的数据
		User user = new User();
		user.setUsername("张三");
		user.setBirthday(new Date());
		user.setSex("1");
		user.setAddress("北京市");
		//通过mapper接口添加用户
		userMapper.insertUser(user);
		//提交
		session.commit();
		//关闭session
		session.close();
	}
	

}
```

