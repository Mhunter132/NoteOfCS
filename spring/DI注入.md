# 配置文件介绍

#### bean标签解析

**bean标签**：指定创建的实体类

​	**id**属性：可以为任意值

​	**class**属性：要实例化类的全限定名

```java
<bean id="abc" class="全包名" scope="single/prototyte" init-method="init" destory-method="end"></bean> 
ApplicationContext context = new ClassPathXmlApplicationContext();
User user = (User)context.getBean("abc");
```

**scope属性**：**singleton 单实例**  ,如果是单实例配置文件一家在就会创建对象，放在spring容器中，以map形式存储，map<key ,value> id是key,value是他的对象

​		     **prototype 多实例**，如果是多实例，配置文件加载，不会创建对象，当每个人getBean时候创建对象，get一次创建一次。

##### 什么时候用单实例，什么时候用多实例：action:prototype

​								**service/dao:singleton**

**single对象什么时候销毁？**

​	答：single对象一般不销毁，当容器关闭。prototype的对象长时间不用就被jvm自动回收。

**init-method:**指定初始化时机

**destory-method：**死指定销毁方法。

##### 导入外部配置文件

​	<import：导入外部配置文件，resource：外部配置文件地址/>

## bean创建的是三种方式

#### 无参数构造方式

​	<bean id="user" class="cn.sxt.domin.User">

#### 静态工厂创建方式

​	条件：需要有个工厂类，还需要有个静态方法，

```java
public class BeanFactory｛
	public static User createUser(){
        return new User();
	}
｝//静态方法负责创建对象
```

<bean id="user" class="cn.sxt.untils.BeanFactory" factory-method="createUser"/>

#### 实例工厂

​	条件：需要有一个工厂类，在这个工厂类中还需要有一个普通方法。

```java
public class BeanFactory｛
	public static User createUser(){
        return new User();
   }
    public User createUser（）｛
     return new User();
   ｝
｝//静态方法负责创建对象

```

<bean id="beanFactory" class="cn.sxt.utils.BeanFactory"></bean>

<bean id="user" factory-bean="beanFactory" factory-method="createUser">

## DI属性注入方式

#### 第一种方式：构造器方式注入

要构造的类中有构造器方法

<bean id="car1" class="cn.sxt.domain.Carimpl">

<!-- name要赋值的属性名 value：值（string和基本类型） ref:引用类型-->

​	<constructor-arg name="name"  value="BMW"></constructor-arg>

<constructor-arg name="price"  value="100"></constructor-arg>

</bean>

#### 第二种方式：set属性注入

```java
class car implements carimpl(){
    string name ;
    integer price;
    getter and setter方法 
}
```

<bean id="car" class="cn.sxt.domain.Carimpl">

<!--property :是set属性的方式 name:要赋值的属性名字 value:要赋的值 ref：引用类型，必须是bean的id-->

<property name="name" value="奇瑞QQ"></property>

</bean>

#### 第三种方式：p命名空间方法（了解）

<!--   DI的注入方式:
	   1 p名称空间的方式
	   条件: 在配置文件中有p的名称空间
	   底层set方式  属性还是得有set方法
   	   语法:
<bean p:属性名="属性值" p:属性名-ref="bean的id对象值" >
	   	 -->

<bean id="person" class="cn.itcast.serviceImpl.PersonImpl" p:name="rose" p:car-ref="car"></bean> 

#### 第四种复杂属性注入：复杂类型

 

```xml
<bean id="collBean" class="cn.itcast.domain.CollBean">
<property name="ss">
    <!--数组类型-->
    <list>
        <value>aaa</value>
        <value>bbb</value>
        <value>ccc</value>
    </list>
</property>
<property name="ll">
    <!--list类型-->
    <list>
    	<value>aaa</value>
        <value>BBB</value>
        <value>ccc</value>
    </list>
 </property>   
    <propety name="mm">
    <!--map-->
        <map>
        	<entry key="k1" value="aaa"></entry>
            <entry key="k2" value="aa"></entry>
            <entry key="k3" value="a"></entry>
        </map>
    </propety>
    <property name="pp">
        <!--property类型-->
    	<props>
        	<prop key="hibernate.name">root</prop>
            <prop key="hibernate.password">123</prop>
        </props>
    </property>




```

![以后代替properties文件](C:\Users\Administrator\Desktop\%5CUsers%5CAdministrator%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5C1563176510126.png)以后代替properties文件

#### springAPI内容

 ioc的AIP内容
	ApplicationContext:接口
	2个常用的实现类:
	ClassPathXmlApplicationContext
		从src下加载配置文件
	FileSystemXmlApplicationContext
		从磁盘路径下加载配置文

​	默认情况:配置文件只要一加载 就创建对象
​		scope:singleton
​					




​		

​	



​	