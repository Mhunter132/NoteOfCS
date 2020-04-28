# SpringMVC开发步骤

## 第一步：导包

## 第二步：配置前端控制器

```xml
在web.xml中添加DispatcherServlet的配置。
<!-- 前端控制器 -->
  <servlet>
  	<servlet-name>springmvc</servlet-name>
  	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  	<init-param>
  		<param-name>contextConfigLocation</param-name>
  		<param-value>classpath:springmvc.xml</param-value>
  	</init-param>
  </servlet>
  <servlet-mapping>
  	<servlet-name>springmvc</servlet-name>
  	<url-pattern>*.action</url-pattern>
  </servlet-mapping>
```

## 第三步：创建springmvc.xml

```xml-dtd
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd">
        	
</beans>
```

## 第四步：创建ItemsController

​      创建handler可以有多种方法，其中比较原始的方法可以实现**Controller接口**，在开发中经常使用的方法有**注解方式**。在此分别演示两种实现方式。

### 4.1实现controller接口

```java
public class ItmesController implements Controller{

	@Override
	public ModelAndView handleRequest(HttpServletRequest request,
			HttpServletResponse response) throws Exception {
		
		List<Items> itemsList = new ArrayList<>();
		
		//商品列表
		Items items_1 = new Items();
		items_1.setName("联想笔记本");
		items_1.setPrice(6000f);
		items_1.setDetail("ThinkPad T430 联想笔记本电脑！");
		
		Items items_2 = new Items();
		items_2.setName("苹果手机");
		items_2.setPrice(5000f);
		items_2.setDetail("iphone6苹果手机！");
		
		itemsList.add(items_1);
		itemsList.add(items_2);
		//创建modelandView对象
		ModelAndView modelAndView = new ModelAndView();
		//添加model
		modelAndView.addObject("itemsList", itemsList);
		//添加视图
		modelAndView.setViewName("/WEB-INF/jsp/itemsList.jsp");
		//modelAndView.setViewName("itemsList");
		
		
		return modelAndView;
	}

}
```

### 4.2注解方式

```java
ItemController是一个普通的java类，不需要实现任何接口，只需要在类上添加@Controller注解即可。@RequestMapping注解指定请求的url，其中“.action”可以加也可以不加。在ModelAndView对象中，将视图设置为“/WEB-INF/jsp/itemList.jsp”
@Controller
public class ItemController {

	@RequestMapping("/itemList")
	public ModelAndView itemList() throws Exception {
		
		List<Items> itemList = new ArrayList<>();
		
		//商品列表
		Items items_1 = new Items();
		items_1.setName("联想笔记本_3");
		items_1.setPrice(6000f);
		items_1.setDetail("ThinkPad T430 联想笔记本电脑！");
		
		Items items_2 = new Items();
		items_2.setName("苹果手机");
		items_2.setPrice(5000f);
		items_2.setDetail("iphone6苹果手机！");
		
		itemList.add(items_1);
		itemList.add(items_2);
		//创建modelandView对象
		ModelAndView modelAndView = new ModelAndView();
		//添加model
		modelAndView.addObject("itemList", itemList);
		//添加视图
		modelAndView.setViewName("/WEB-INF/jsp/itemList.jsp");
//		modelAndView.setViewName("itemsList");	
		return modelAndView;
	}

}
```

## 第五步：将ItemsController配置到springmvc.xml中

1.非注解处理器

非注解处理器类 BeanNameUrlHandlerMapping，这个类的映射规则是将 bean 的 name 作为 url 进行查找，需要在配置 Handler 时指定 beanname，其示例代码如下：

```xml
<!-- 配置Controller将控制器配置到xml文件上 -->
	<bean id="itemsController" name="/itemsList.action" class="cn.itcast.mvc.controller.ItmesController"/>
---------------------------------------------------------------------------------------------------
非注解处理器类 SimpleUrlHandlerMapping，它可以通过内部参数去配置请求的 url 和 handler 之间的映射关系，其示例配置如下：

<bean id="urlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="interceptors">
        <list>
            <ref bean="someCheckInterceptor1"/>
            <ref bean="someCheckInterceptor2"/>
        </list>
    </property>
    <property name="mapping">
        <props>
            <prop key="user.action">userController</prop>
            <prop key="product.action">productController</prop>
            <prop key="other.action">otherController</prop>
        </props>            
    </property>
</bean>
```

##  2.注解映射器和适配器

```xml

使用组件扫描器省去在spring容器配置每个controller类的繁琐。使用<context:component-scan自动扫描标记@controller的控制器类，配置如下：

<!-- 扫描controller注解,多个包中间使用半角逗号分隔 -->
	<context:component-scan base-package="cn.itcast.springmvc.controller.first"/>
---------------------------------------------------------------------------------------------------
<!--注解映射器 -->
	<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/>
```

```xml
<!---第二种方法注解驱动器 -->
< mvc:annotation-driven >

springmvc使用< mvc:annotation-driven>自动加载**RequestMappingHandlerMapping**和**RequestMappingHandlerAdapter**，可用在springmvc.xml配置文件中使用<mvc:annotation-driven >替代注解处理器和适配器的配置。
```

注解描述：

@RequestMapping：定义请求url到处理器功能方法的映射

## 3.视图解析器

在springmvc.xml文件配置如下：

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">		
    <property name="viewClass"			value=*"org.springframework.web.servlet.view.JstlView"* />		
    <property name=*"prefix"* value=*"/WEB-INF/jsp/"* />		<property name=*"suffix"* value=*".jsp"* />	</bean>
```

 

InternalResourceViewResolver：支持JSP视图解析

viewClass：JstlView表示JSP模板页面需要使用JSTL标签库，所以classpath中必须包含jstl的相关jar 包。此属性可以不设置，默认为JstlView。

prefix 和suffix：查找视图页面的前缀和后缀，最终视图的址为：

前缀+**逻辑视图名**+后缀，逻辑视图名需要在controller中返回ModelAndView指定，比如逻辑视图名为hello，则最终返回的jsp视图地址 “WEB-INF/jsp/hello.jsp”

### 3.1整合mybatis

Dao层：

​	1、SqlMapConfig.xml，空文件即可。需要文件头。

​	2、applicationContext-dao.xml。

​	a) 数据库连接池

​	b) SqlSessionFactory对象，需要spring和mybatis整合包下的。

​	c) 配置mapper文件扫描器。

Service层：

​	1、applicationContext-service.xml包扫描器，扫描@service注解的类。

​	2、applicationContext-trans.xml配置事务。

表现层：

​	Springmvc.xml

​	1、包扫描器，扫描@Controller注解的类。

​	2、配置注解驱动。

​	3、视图解析器

Web.xml

​	配置前端控制器。

#### sqlMapConfig.xml

```xml

在classpath下创建mybatis/sqlMapConfig.xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
</configuration>
```

#### applicationContext-dao.xml

配置数据源、配置SqlSessionFactory、mapper扫描器。

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">

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
	<!-- 配置Mapper扫描器 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="cn.sxt.springmvc.mapper"/>
	</bean>

</beans>
```

#### applicationContext-service.xml

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">

	<context:component-scan base-package="cn.itcast.springmvc.service"/>

</beans>
```

#### applicationContext-transaction.xml

```xml

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">
	<!-- 事务管理器 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>
	<!-- 通知 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 传播行为 -->
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="insert*" propagation="REQUIRED" />
			<tx:method name="delete*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
			<tx:method name="find*" propagation="SUPPORTS" read-only="true" />
			<tx:method name="get*" propagation="SUPPORTS" read-only="true" />
		</tx:attributes>
	</tx:advice>
	<!-- 切面 -->
	<aop:config>
		<aop:advisor advice-ref="txAdvice"
			pointcut="execution(* cn.itcast.springmvc.service.*.*(..))" />
	</aop:config>
</beans>
```

#### springmvc.xml

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd">
        
</beans>
```

#### web.xml

```xml

<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://java.sun.com/xml/ns/javaee"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
	id="WebApp_ID" version="2.5">
	<display-name>springmvc-web</display-name>
	<welcome-file-list>
		<welcome-file>index.html</welcome-file>
		<welcome-file>index.htm</welcome-file>
		<welcome-file>index.jsp</welcome-file>
		<welcome-file>default.html</welcome-file>
		<welcome-file>default.htm</welcome-file>
		<welcome-file>default.jsp</welcome-file>
	</welcome-file-list>
	<!-- 加载spring容器 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring/applicationContext-*.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	
	<servlet>
		<servlet-name>springmvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:spring/springmvc.xml</param-value>
		</init-param>
	</servlet>
	<servlet-mapping>
		<servlet-name>springmvc</servlet-name>
		<url-pattern>*.action</url-pattern>
	</servlet-mapping>
</web-app>
```

##  4.参数绑定

### 4.1默认支持的参数类型

#### 	HttpServletRequest

#### 	HttpServletResponse

#### 	HttpSession

#### 	Model/ModelMap

### 4.2绑定简单类型

当请求的参数名称和处理器形参**名称一致**时会将请求参数与形参进行绑定。从Request取参数的方法可以进一步简化。

###  **4.3@RequestParam** 

使用@RequestParam常用于处理简单类型的绑定。

value：参数名字，即入参的请求参数名字，如value=“item_id”表示请求的参数区中的名字为item_id的参数的值将传入；

required：是否必须，默认是true，表示请求中一定要有相应的参数，否则将报；

TTP Status 400 - Required Integer parameter 'XXXX' is not present

defaultValue：默认值，表示如果请求中没有同名参数时的默认值

定义如下：

public String editItem(@RequestParam(value="item_id",required=true) String id) {

} 

参名称为id，但是这里使用value=" item_id"限定请求的参数名为item_id，所以页面传递参数的名必须为item_id。

注意：如果请求参数中没有item_id将跑出异常：

HTTP Status 500 - Required Integer parameter 'item_id' is not present

这里通过required=true限定item_id参数为必需传递，如果不传递则报400错误，可以使用defaultvalue设置默认值，即使required=true也可以不传item_id参数值

### 4.4绑定pojo类型 

​	如果提交的参数很多，或者提交的表单中的内容很多的时候可以使用pojo接收数据。要求pojo对象中的属性名和表单中input的name属性一致。

###  **4.5解决post乱码问题**

在web.xml中加入：

```xml
< filter >		
	< filter-name>CharacterEncodingFilter
< /filter-name>		
< filter-class>org.springframework.web.filter.CharacterEncodingFilter< /filter-class>		
	< init-param>			
	< param-name>encoding< /param-name>			
	< param-value>utf-8</ param-value>		
	< /init-param>	
< /filter>	
< filter-mapping>		
	< filter-name>CharacterEncodingFilter< /filter-name>		
	< url-pattern>/*< /url-pattern>	
< /filter-mapping >
```

以上可以解决post请求乱码问题。

对于get请求中文参数出现乱码解决方法有两个：

修改tomcat配置文件添加编码与工程编码一致，如下：

< Connector URIEncoding="utf-8" connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443" />

另外一种方法对参数进行重新编码：

String userName new 

String(request.getParamter("userName").getBytes("ISO8859-1"),"utf-8")

ISO8859-1是tomcat默认编码，需要将tomcat编码后的内容按utf-8编码

## 5.自定义参数绑定

### 5.1需求

在商品修改页面可以修改商品的生产日期，并且根据业务需求自定义日期格式。由于日期数据有很多种格式，所以springmvc没办法把字符串转换成日期类型。所以需要自定义参数绑定。前端控制器接收到请求后，找到注解形式RequestMapping的处理器适配器，对方法中的形参进行参数绑定。在springmvc这可以在处理器适配器上自定义Converter进行参数绑定。如果使用<mvc:annotation-driven/>可以在此标签上进行扩展。

### 5.2自定义Converter

```java
public class DateConverter implements Converter<String, Date> {

	@Override
	public Date convert(String source) {
		SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		try {
			return simpleDateFormat.parse(source);
		} catch (ParseException e) {
			e.printStackTrace();
		}
		return null;
	}
}
```

### 5.3配置Converter



```xml
	<!-- 加载注解驱动 -->	
<mvc:annotation-driven conversion-service=*"conversionService"*/>	
<!-- 转换器配置 -->	
<bean id=*"conversionService"*		class=*"org.springframework.format.support.FormattingConversionServiceFactoryBean"*>			     <property name=*"converters"*>		
       <set>				
        <bean class=*"**cn.itcast.springmvc.convert.DateConverter**"*/>			
       </set>		
    </property>
</bean>
```

 

### 5.4配置方式

在pojo类的日期属性上添加如下注解：

@DateTimeFormat(pattern = "yyyy-MM-dd")

### 5.5绑定数组

#### 5.5.1需求分析

此功能要求商品列表页面中的每个商品前有一个checkbook，选中多个商品后点击删除按钮把商品id传递给Controller，根据商品id删除商品信息。

#### 5.5.2Controller

Controller方法中可以用String[]接收，或者pojo的String[]属性接收。两种方式任选其一即可。

定义如下：

```java
@RequestMapping("/queryitem")	**public** String queryItem(QueryVo queryVo, String[] ids) {		System.**out**.println(queryVo.getItems().getName());		System.**out**.println(queryVo.getItems().getPrice());		System.**out**.println(ids.toString());		**return** **null**;	}
```

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5472\wps1.jpg)

### 5.6将表单的数据绑定到List

#### 5.6.1需求分析

要想实现商品数据的批量修改，需要在商品列表中可以对商品信息进行修改，并且可以批量提交修改后的商品数据。

####  5.6.2接收商品列表的pojo

List中存放对象，并将定义的List放在包装类中，使用包装pojo对象接收。

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5472\wps2.jpg)

```xml
Name属性必须是包装pojo的list属性+下标+元素属性。Jsp做如下改造：
<c:forEach items="${itemList }" var="item" varStatus="status">
<tr>
	<td>
		<input type="checkbox" name="ids" value="${item.id }">
		<input type="hidden" name="itemList[${status.index }].id" value="${item.id }">
	</td>
	<td><input type="text" name="itemList[${status.index }].name" value="${item.name }"></td>
	<td><input type="text" name="itemList[${status.index }].price" value="${item.price }"></td>
	<td><input type="text" name="itemList[${status.index }].createtime" value="<fmt:formatDate value="${item.createtime}" pattern="yyyy-MM-dd HH:mm:ss"/>"></td>
	<td><input type="text" name="itemList[${status.index }].detail" value="${item.detail }"></td>
	
	<td><a href="${pageContext.request.contextPath }/item/itemEdit.action?id=${item.id}">修改</a></td>

</tr>
</c:forEach>
        varStatus属性常用参数总结下：
current: 当前这次迭代的（集合中的）项
index  : 当前这次迭代从 0 开始的迭代索引
count  : 当前这次迭代从 1 开始的迭代计数
first  : 用来表明当前这轮迭代是否为第一次迭代的标志
last   : 用来表明当前这轮迭代是否为最后一次迭代的标志
begin  : 返回begin属性值
end    : 返回end属性值
step   : 返回step属性值
${status.index}      输出行号，从0开始。
${status.count}      输出行号，从1开始。
${status.current}   当前这次迭代的（集合中的）项
${status.first}  判断当前项是否为集合中的第一项，返回值为true或false
${status.last}   判断当前项是否为集合中的最后一项，返回值为true或false
begin、end、step分别表示：起始序号，结束序号，跳跃步伐。
```

#### 5.6.3Contrller

```java

@RequestMapping("/queryitem")
	public String queryItem(QueryVo queryVo, String[] ids) {
		System.out.println(queryVo.getItems().getName());
		System.out.println(queryVo.getItems().getPrice());
		System.out.println(ids.toString());
		return null;
	}
```





























