# **IceBreanker_of_Mybatis**

## parameterType(输入类型)

 基础数据类型
      pojo
      map
      包装的pojo----用户里面装订单

## resultType(输出类型)

 基础数据类型
      pojo
      list
      map

## resultMap

​	resultType可以指定pojo将查询结果映射为pojo，但需要pojo的属性名和sql查询的列名一致方可映射成功。

​	如果sql查询字段名和pojo的属性名不一致，可以通过resultMap将字段名和属性名作一个对应关系 ，resultMap实质上还需要将查询结果映射到pojo对象中。

​	resultMap可以实现将查询结果映射为复杂类型的pojo，比如在查询结果映射对象中包括pojo和list实现一对一查询和一对多查询。

![1563503263740](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563503263740.png)

**</id/>**：此属性表示查询结果集的唯一标识，非常重要。如果是多个字段为复合唯一约束则定义多个<id />。

**Property：**表示User类的属性。

**Column：**表示sql查询出来的字段名。

**Column和property**放在一块儿表示将sql查询出来的字段映射到指定的pojo类属性上。

**</result />**：普通结果，即pojo的属性。

## 动态sql

#### if

```xml
<!-- 传递pojo综合查询用户信息 -->
<select id="findUserList" parameterType="user" resultType="user">
		select * from user 
		where 1=1 
		<if test="id!=null">
		and id=#{id}
		</if>
		<if test="username!=null and username!=''">
		and username like '%${username}%'
		</if>
</select>
```

#### where

```xml
<select id="findUserList" parameterType="user" resultType="user">
		select * from user 
		<where>
		<if test="id!=null and id!=''">
		and id=#{id}
		</if>
		<if test="username!=null and username!=''">
		and username like '%${username}%'
		</if>
		</where>
</select>
```

#### foreach

传入多个id查询用户信息，用下边两个sql实现：

SELECT * FROM USERS WHERE username LIKE '%张%' AND (id =10 OR id =89 OR id=16)

SELECT * FROM USERS WHERE username LIKE '%张%'  id  IN (10,89,16)

![1563504844844](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563504844844.png)

#### mapper.xml

```xml
<if test="ids!=null and ids.size>0">
	    	<foreach collection="ids" open=" and id in(" close=")" item="id" separator="," >
	    		#{id}
	    	</foreach>
</if>

```

#### 测试

```java

List<Integer> ids = new ArrayList<Integer>();
		ids.add(1);//查询id为1的用户
		ids.add(10); //查询id为10的用户
		queryVo.setIds(ids);
		List<User> list = userMapper.findUserList(queryVo);
```

## sql片段

Sql中可将重复的sql提取出来，使用时用include引用即可，最终达到sql重用的目的，如下：

```xml
<!-- 传递pojo综合查询用户信息 -->
<select id="findUserList" parameterType="user" resultType="user">
		select * from user 
		<where>
		<if test="id!=null and id!=''">
		and id=#{id}
		</if>
		<if test="username!=null and username!=''">
		and username like '%${username}%'
		</if>
		</where>
</select>

将where条件抽取出来：

<sql id="query_user_where">
	<if test="id!=null and id!=''">
		and id=#{id}
	</if>
	<if test="username!=null and username!=''">
		and username like '%${username}%'
	</if>
</sql>

使用include引用：

<select id="findUserList" parameterType="user" resultType="user">
		select * from user 
		<where>
		<include refid="query_user_where"/>
		</where>
	</select>

注意：如果引用其它mapper.xml的sql片段，则在引用时需要加上namespace，如下：
<include refid="namespace.sql片段”/>
```

## 一对一查询

```xml
<!---方法一-->
<!---方法二-->
<resultMap  id="ordersResultMap" type="orders">
     <id column="id" property="id"/>
     <result column="user_id" property="user_id"/>
     <result column="number" property="number"/>
     <result column="createtime" property="createtime"/>
     <result column="node" property="node"/>
     
<!--      构建一对一关联关系      -->
     <association property="user" javaType="cn.itcast.mybatis.pojo.User">
      <id column="user_id" property="id"/>
      <result column="username" property="username"/>
      <result column="birthday" property="birthday"/>
      <result column="sex" property="sex"/>
      <result column="address" property="address"/>
     </association>
          
   </resultMap>
```

pojo

```java
package cn.itcast.mybatis.pojo;

import java.util.Date;

public class Orders {
	
	private int id;
	private int user_id;
	private String number;
	private Date createtime;
	private String node;
	
	private User user;
	
	
	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public int getUser_id() {
		return user_id;
	}
	public void setUser_id(int user_id) {
		this.user_id = user_id;
	}
	public String getNumber() {
		return number;
	}
	public void setNumber(String number) {
		this.number = number;
	}
	public Date getCreatetime() {
		return createtime;
	}
	public void setCreatetime(Date createtime) {
		this.createtime = createtime;
	}
	public String getNode() {
		return node;
	}
	public void setNode(String node) {
		this.node = node;
	}
}
-----------------------------------------------------
    package cn.itcast.mybatis.pojo;

import java.io.Serializable;
import java.util.Date;
import java.util.List;

public class User implements Serializable {
	private int id;
	private String username;// 用户姓名
	private String sex;// 性别
	private Date birthday;// 生日
	private String address;// 地址
	
	private List<Orders> ordersList;

	public List<Orders> getOrdersList() {
		return ordersList;
	}
	public void setOrdersList(List<Orders> ordersList) {
		this.ordersList = ordersList;
	}
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public Date getBirthday() {
		return birthday;
	}
	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
	@Override
	public String toString() {
		return "User [id=" + id + ", username=" + username + ", sex=" + sex + ", birthday=" + birthday + ", address="
				+ address + "]";
	}
}

```

这里resultMap指定ordersUserMap

association：表示进行关联查询单条记录

property：表示关联查询的结果存储在cn.itcast.mybatis.pojo.Orders的user属性中

javaType：表示关联查询的结果类型

<id property=*"id"* column=*"user_id"*/>：查询结果的user_id列对应关联对象的id属性，这里是<id />表示user_id是关联查询对象的唯一标识。

<result property=*"username"* column=*"username"*/>：查询结果的username列对应关联对象的username属性。

## 一对多查询

​       即使返回结果是list，mybatis不知道list里面返回的是什么类型，mybatis可以检测多条结果进行组装，但是不知道里面什么类型，所以我们需要指定类型

### pojo

![1563505912540](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563505912540.png)

### mapper.xml

```xml
<resultMap type="user" id="userOrderResultMap">
		<!-- 用户信息映射 -->
		<id property="id" column="id"/>
		<result property="username" column="username"/>
		<result property="birthday" column="birthday"/>
		<result property="sex" column="sex"/>
		<result property="address" column="address"/>
		<!-- 一对多关联映射 -->
		<collection property="orders" ofType="orders">
			<id property="id" column="oid"/>	
		      <!--用户id已经在user对象中存在，此处可以不设置-->
			<!-- <result property="userId" column="id"/> -->
			<result property="number" column="number"/>
			<result property="createtime" column="createtime"/>
			<result property="note" column="note"/>
		</collection>
	</resultMap>
	<select id="getUserOrderList" resultMap="userOrderResultMap">
		SELECT
		u.*, o.id oid,
		o.number,
		o.createtime,
		o.note
		FROM
		`user` u
		LEFT JOIN orders o ON u.id = o.user_id
	</select>
	
```

collection部分定义了用户关联的订单信息。表示关联查询结果集

property=*"**orders**"：*关联查询的结果集存储在User对象的上哪个属性。

ofType=*"**orders**"：*指定关联查询的结果集中的对象类型即List中的对象类型。此处可以使用别名，也可以使用全限定名。

</id />及</result/>的意义同一对一查询。

###  Mapper接口：

```java
List<  User > getUserOrderList();
```



### 测试

```java
@Test
	public void getUserOrderList() {
		SqlSession session = sqlSessionFactory.openSession();
		UserMapper userMapper = session.getMapper(UserMapper.class);
		List<User> result = userMapper.getUserOrderList();
		for (User user : result) {
			System.out.println(user);
		}
		session.close();
	}
```

## mybatis和spring整合思路

第一步：创建一个java工程。

第二步：导入jar包。（上面提到的jar包）

第三步：mybatis的配置文件sqlmapConfig.xml

第四步：编写Spring的配置文件

1、数据库连接及连接池

2、事务管理（暂时可以不配置）

3、sqlsessionFactory对象，配置到spring容器中

4、mapper代理对象或者是dao实现类配置到spring容器中。

第五步：编写dao或者mapper文件

第六步：测试。

### 1.SqlMapConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<typeAliases>
		<package name="cn.itcast.mybatis.pojo"/>
	</typeAliases>
	<mappers>
		<mapper resource="sqlmap/User.xml"/>
	</mappers>
</configuration>
```

### **2.applicationContext.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd">

	<!-- 加载配置文件 -->
	<context:property-placeholder location="classpath:db.properties" />
	<!-- 数据库连接池 -->
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<property name="maxActive" value="10" />
		<property name="maxIdle" value="5" />
	</bean>
	<!-- mapper配置 -->
	<!-- 让spring管理sqlsessionfactory 使用mybatis和spring整合包中的 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 数据库连接池 -->
		<property name="dataSource" ref="dataSource" />
		<!-- 加载mybatis的全局配置文件 -->
		<property name="configLocation" value="classpath:mybatis/SqlMapConfig.xml" />
	</bean>
    <!--
	将mybatis标签也交给spring管理
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">  
       <property name="dataSource" ref="dataSource" />  
       <property name="mapperLocations"  
              value="classpath:com/tiantian/ckeditor/mybatis/mappers/*Mapper.xml" />  
       <property name="typeAliasesPackage" value="com.tiantian.ckeditor.model" />  
       -------typeAliasesPackage的value是包名，typeAliases的value是；类名----------------------------
	  <property name="typeAliases">  
          <array>  
     	      <value>com.tiantian.mybatis.model.Blog</value>  
       		 <value>com.tiantian.mybatis.model.Comment</value>  
          </array>  
       </property>  

    </bean>
-->


</beans>
```

## 传统Dao层

接口+实现类来完成。需要dao实现类需要继承SqlsessionDaoSupport类

### DAO实现类

```java
public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {

	@Override
	public User findUserById(int id) throws Exception {
		SqlSession session = getSqlSession();
		User user = session.selectOne("test.findUserById", id);
		//不能关闭SqlSession，让spring容器来完成
		//session.close();
		return user;
	}

	@Override
	public void insertUser(User user) throws Exception {
		SqlSession session = getSqlSession();
		session.insert("test.insertUser", user);
		//session.commit();
		//session.close();
	}

}
```

### 配置dao

```xml
把dao实现类配置到spring容器中
<!-- 配置UserDao实现类 -->
	<bean id="userDao" class="cn.sxt.dao.UserDaoImpl"> <!--类-->
		<property name="sqlSessionFactory" ref="sqlSessionFactory"/>
	</bean>
```

### 测试方法

```java
@Test
	public void testFindUserById() throws Exception {
		UserDao userDao = (UserDao) applicationContext.getBean("userDao");
		User user = userDao.findUserById(1);
		System.out.println(user);
	}
```

## Mappe代理模式开发

### 书写接口文件+配置文件

### 配置mapper代理（spring）

```xml
<!-- 配置mapper代理对象 -->
	<bean class="org.mybatis.spring.mapper.MapperFactoryBean">
		<property name="mapperInterface" value="cn.itcast.mybatis.mapper.UserMapper"/>
		<property name="sqlSessionFactory" ref="sqlSessionFactory"></property>
	</bean>
```

### 测试方法

```java
public class UserMapperTest {

	private ApplicationContext applicationContext;
	@Before
	public void setUp() throws Exception {
		applicationContext = new ClassPathXmlApplicationContext("classpath:spring/applicationContext.xml");
	}

	@Test
	public void testGetUserById() {
		UserMapper userMapper = applicationContext.getBean(UserMapper.class);
		User user = userMapper.getUserById(1);
		System.out.println(user);
	}

}
```

### 扫描包形式配置mapper

```xml
<!-- 使用扫描包的形式来创建mapper代理对象，
每个mapper代理对象的id就是类名，首字母小写-->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="cn.itcast.mybatis.mapper"></property>
	</bean>
```

![1563518342575](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563518342575.png)

## 逆向工程

```
使用官方网站的mapper自动生成工具mybatis-generator-core-1.3.2来生成pojo类和mapper映射文件。

1第一步：mapper生成配置文件：
在generatorConfig.xml中配置mapper生成的详细信息，注意改下几点：

1、添加要生成的数据库表
2、pojo文件所在包路径
3、mapper文件所在包路径

配置文件如下：
详见generatorSqlmapCustom工程

2第二步：使用java类生成mapper文件：

Public void generator() throws Exception{
		List<String> warnings = new ArrayList<String>();
		boolean overwrite = true;
		File configFile = new File("generatorConfig.xml"); 
		ConfigurationParser cp = new ConfigurationParser(warnings);
		Configuration config = cp.parseConfiguration(configFile);
		DefaultShellCallback callback = new DefaultShellCallback(overwrite);
		MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
				callback, warnings);
		myBatisGenerator.generate(null);
	}
	Public static void main(String[] args) throws Exception {
		try {
			GeneratorSqlmap generatorSqlmap = new GeneratorSqlmap();
			generatorSqlmap.generator();
		} catch (Exception e) {
			e.printStackTrace();
		}
}



3第三步：拷贝生成的mapper文件到工程中指定的目录中
3.1Mapper.xml
Mapper.xml的文件拷贝至mapper目录内
3.2Mapper.java
Mapper.java的文件拷贝至mapper 目录内


注意：mapper xml文件和mapper.java文件在一个目录内且文件名相同。


3.3第四步Mapper接口测试

学会使用mapper自动生成的增、删、改、查方法。

//删除符合条件的记录
int deleteByExample(UserExample example);
//根据主键删除
int deleteByPrimaryKey(String id);
//插入对象所有字段
int insert(User record);
//插入对象不为空的字段
int insertSelective(User record);
//自定义查询条件查询结果集
List<User> selectByExample(UserExample example);
//根据主键查询
UserselectByPrimaryKey(String id);
//根据主键将对象中不为空的值更新至数据库
int updateByPrimaryKeySelective(User record);
//根据主键将对象中所有字段的值更新至数据库
int updateByPrimaryKey(User record);

4逆向工程注意事项
4.1Mapper文件内容不覆盖而是追加
XXXMapper.xml文件已经存在时，如果进行重新生成则mapper.xml文件内容不被覆盖而是进行内容追加，结果导致mybatis解析失败。
解决方法：删除原来已经生成的mapper xml文件再进行生成。
Mybatis自动生成的pojo及mapper.java文件不是内容而是直接覆盖没有此问题。

```

