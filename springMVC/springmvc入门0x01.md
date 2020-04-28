## @RequestMapping

@RequestMapping(value="/item")或@RequestMapping("/item）

value的值是数组，可以将多个url映射到同一个方法.

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5472\wps3.jpg)

#### 窄化请求映射

在class上添加@RequestMapping(url)指定通用请求前缀， 限制此类下的所有方法请求url必须以请求前缀开头，通过此方法对url进行分类管理。

如下：

@RequestMapping放在类名上边，设置请求前缀 

@Controller

@RequestMapping("/item")

方法名上边设置请求映射url：

@RequestMapping放在方法名上边，如下：

@RequestMapping("/queryItem ")

#### 请求方法限定

限定GET方法

@RequestMapping(method = RequestMethod.*GET*)

限定POST方法

@RequestMapping(method = RequestMethod.*POST*)

GET和POST都可以

@RequestMapping(method={RequestMethod.GET,RequestMethod.POST})

## controller方法返回类型

#### 返回ModelAndView

controller方法中定义ModelAndView对象并返回，对象中可添加model数据、指定view

#### 返回void

传统的servlet跳转

#### 返回字符串

##### 逻辑视图名

 	controller方法返回字符串可以指定逻辑视图名，通过视图解析器解析为物理视图地址。

//指定逻辑视图名，经过视图解析器解析为jsp物理路径：/WEB-INF/jsp/item/editItem.jsp

**return** "item/editItem";

#### Redirect重定向

Contrller方法返回结果重定向到一个url地址，如下商品修改提交后重定向到商品查询方法，参数无法带到商品查询方法中。

//重定向到list.action地址,request无法带过去

**return** "redirect:list.action";

redirect方式相当于“response.sendRedirect()”，转发后浏览器的地址栏变为转发后的地址，因为转发即执行了一个新的request和response。

由于新发起一个request原来的参数在转发时就不能传递到下一个url，如果要传参数可以/item/list.action后边加参数，如下：

/item/list?...&…..

#### forward转发

controller方法执行后继续执行另一个controller方法，如下商品修改提交后转向到商品修改页面，修改商品的id参数可以带到商品修改方法中。

//结果转发到editItem.action，request可以带过去

**return** "forward:editItem.action";

#### forward方式

使用原request域。

## 上传图片

1.配置解析器

```xml
<!-- 文件上传 -->
	<bean id="multipartResolver"
		class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<!-- 设置上传文件的最大尺寸为5MB -->
		<property name="maxUploadSize">
			<value>5242880</value>
		</property>
     </bean>
```

2.controller

```java
//商品修改提交
	@RequestMapping("/editItemSubmit")
	public String editItemSubmit(Items items, MultipartFile pictureFile)throws Exception{
		
		//原始文件名称
		String pictureFile_name =  pictureFile.getOriginalFilename();
		//新文件名称
		String newFileName = UUID.randomUUID().toString()+pictureFile_name.substring(pictureFile_name.lastIndexOf("."));
		
		//上传图片
		File uploadPic = new java.io.File("F:/develop/upload/temp/"+newFileName);
		
		if(!uploadPic.exists()){
			uploadPic.mkdirs();
		}
		//向磁盘写文件
		pictureFile.transferTo(uploadPic);

.....

```

页面

```xml
form添加enctype="multipart/form-data"：
<form id="itemForm"
action="${pageContext.request.contextPath }/item/editItemSubmit.action"
		method="post" enctype="multipart/form-data">
		<input type="hidden" name="pic" value="${item.pic }" />
    
    <!--file的name与controller形参一致：-->
   
<tr>
	<td>商品图片</td>
	<td><c:if test="${item.pic !=null}">
			<img src="/pic/${item.pic}" width=100 height=100 />
			<br />
		</c:if> <input type="file" name="pictureFile" /></td>
</tr>

```

## 异常处理器

​	Web工程中一般都是三层结构，如果系统中无论那一层发送异常都可以直接向上抛出 , 最后由springmvc前端控制器交由异常处理器进行异常处理

### 自定义异常处理器

要实现 **HandlerExceptionResolver** 接口。

```java
public class CustomExceptionResolver implements HandlerExceptionResolver {

	@Override
	public ModelAndView resolveException(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex) {

		ex.printStackTrace();
	
		ModelAndView modelAndView = new ModelAndView();
		modelAndView.addObject("message", customException.getMessage());
		modelAndView.setViewName("error");

		return modelAndView;
	}

}

取异常堆栈：
           try {
			
		} catch (Exception e) {
			StringWriter s = new StringWriter();
			PrintWriter printWriter = new PrintWriter(s);
			e.printStackTrace(printWriter);
			s.toString();
		}
```

#### 异常处理器配置

```xml
<!-- 异常处理器 -->
	<bean id="handlerExceptionResolver" class="cn.itcast.ssm.controller.exceptionResolver.CustomExceptionResolver"/>
```

##  json数据交互

## @RequestBody

必须传过来json数据。

## @ResponseBody

该注解用于将Controller的方法返回的对象，通过HttpMessageConverter接口转换为指定格式的数据如：json,xml等，通过Response响应给客户端

## 请求json，响应json实现：

### 环境准备

Springmvc默认用MappingJacksonHttpMessageConverter对json数据进行转换，需要加入jackson的包，如下：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5472\wps4.jpg) 

### 配置json转换器

在注解适配器中加入messageConverters

```xml
<!--注解适配器 -->

	<bean class=*"org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"*>

		<property name=*"messageConverters"*>

		< list>

		<bean class=*"org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"*></bean>

		</list>

		</property>

	</bean>
```

 

注意：如果使用<mvc:annotation-driven /> **则不用定义上边的内容。**

#### controller编写

```java
// 商品修改提交json信息，响应json信息

	@RequestMapping("/editItemSubmit_RequestJson")
	@ResponseBody
	@RequestBody	
	public  Items editItemSubmit_RequestJson( Items items) throws Exception {

		System.out.println(items);

		//itemService.saveItem(items);

		return items;
	}
```

 

### 页面js方法编写：

```xml
引入 js：

<script type="text/javascript" 

src="${pageContext.request.contextPath }/js/jquery-1.4.4.min.js"></script>

//请求json响应json

	function request_json(){
		$.ajax({
			type:"post",
			url:"${pageContext.request.contextPath }/item/editItemSubmit_RequestJson.action",
			contentType:"application/json;charset=utf-8",
			data:'{"name":"测试商品","price":99.9}',
			success:function(data){
				alert(data);
			}
			});
	}
<script type=*"text/javascript"*>	
   var item;
   function getitem() {		
    $.get("${pageContext.request.contextPath }/item/getitem.action",{id:1},**function**(data){			item = data;
         alert(item.name);		});	}
    function sendJson() {		
    item.name="测试商品";
    $.ajax({	
    type:"post",			
    contentType:"application/json;charset=utf-8",
    url:"${pageContext.request.contextPath
    }
    /item/sendjson.action",	
    data:JSON.stringify(item),
    success:function(data) {	
    alert(data.name);		
    }		});	}
</script>
```

<script type=*"text/javascript"*>	**var** item;	**function** getitem() {		$.get("${pageContext.request.contextPath }/item/getitem.action",{id:1},**function**(data){			item = data;			alert(item.name);		});	}	**function** sendJson() {		item.name="测试商品";		$.ajax({			type:"post",			contentType:"application/json;charset=utf-8",			url:"${pageContext.request.contextPath }/item/sendjson.action",			data:JSON.stringify(item),			success:**function**(data) {				alert(data.name);			}		});	}</script>

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5472\wps5.jpg) 

从上图可以看出请求的数据是json格式

### RESTful支持

#### 什么是restful？

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格，是对http协议的诠释。

资源定位：互联网所有的事物都是资源，要求url中没有动词，只有名词。没有参数

Url格式：<http://blog.csdn.net/beat_the_world/article/details/45621673>

资源操作：使用put、delete、**post、get**，使用不同方法对资源进行操作。分别对应添加、删除、修改、查询。一般使用时还是post和get。Put和Delete几乎不使用。

#### 需求

RESTful方式实现商品信息查询，返回json数据

####  添加DispatcherServlet的rest配置

```xml
	<servlet>

		<servlet-name>springmvc-servlet-rest</servlet-name>

		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>

		<init-param>

			<param-name>contextConfigLocation</param-name>

			<param-value>classpath:spring/springmvc.xml</param-value>

		</init-param>

	</servlet>

	<servlet-mapping>

		<servlet-name>springmvc-servlet-rest</servlet-name>

		<url-pattern>/</url-pattern>

	</servlet-mapping>
```



## URL 模板模式映射

```java
    @RequestMapping(value="/ viewItems/{id}")：{×××}占位符，请求的URL可以                      是“/viewItems/1”或“/viewItems/2”，通过在方法中使用@PathVariable获取{×××}中的×××变量。
    @PathVariable用于将请求URL中的模板变量映射到功能处理方法的参数上。
    @RequestMapping("/viewItems/{id}") 
    @ResponseBody
	public  viewItems(@PathVariable("id") String id,Model model) throws Exception{
		//方法中使用@PathVariable获取useried的值，使用model传回页面
		//调用 service查询商品信息
		ItemsCustom itemsCustom = itemsService.findItemsById(id);
		return itemsCustom；
}
如果RequestMapping中表示为"/viewItems/{id}"，id和形参名称一致，@PathVariable不用指定名称。
商品查询的controller方法也改为rest实现：
// 查询商品列表
	@RequestMapping("/queryItem/{id}")
	public ModelAndView queryItem(@pathvariable int id) throws Exception {
		// 商品列表
		List<Items> itemsList = itemService.findItemsList(null);
		// 创建modelAndView准备填充数据、设置视图
		ModelAndView modelAndView = new ModelAndView();
		// 填充数据
		modelAndView.addObject("itemsList", itemsList);
		// 视图
		modelAndView.setViewName("item/itemsList");
		return modelAndView;
	}
```



### 静态资源访问 

<!-- 静态资源访问 -->
 **一 :**<  mvc:default-servlet-handler/ >

以下两种在SpringMVC3.0之前可以使用

**二**：

```xml
  <!-- 静态资源访问
  <mvc:resources location="/img/" mapping="/img/**"/> 
  <mvc:resources location="/js/" mapping="/js/**"/>  
  <mvc:resources location="/css/" mapping="/css/**"/>
好处就是可以限制访问静态资源 
-->
```

**三：**

web.xml里添加如下的配置

```xml
<servlet-mapping>
     <servlet-name>default</servlet-name>
     <url-pattern>*.css</url-pattern>
</servlet-mapping>

<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.gif</url-pattern>

</servlet-mapping>

<servlet-mapping>
     <servlet-name>default</servlet-name>
     <url-pattern>*.jpg</url-pattern>
</servlet-mapping>

<servlet-mapping>
     <servlet-name>default</servlet-name>
     <url-pattern>*.js</url-pattern>
</servlet-mapping>
```

​        如果在DispatcherServlet中设置url-pattern为 / 则必须对静态资源进行访问处理。

spring mvc 的<mvc:resources mapping=" " location="">实现对静态资源进行映射访问。

如下是对js文件访问配置：

<mvc:resources location="/js/" mapping="/js/**"/>

## 拦截器

### 定义

​	Spring Web MVC 的处理器拦截器类似于Servlet 开发中的过滤器Filter，用于对处理器进行预处理和后处理

### 拦截器定义

实现 **HandlerInterceptor** 接口，如下：

```java
Public class HandlerInterceptor1 implements HandlerInterceptor{
	/**
	 * controller执行前调用此方法
	 * 返回true表示继续执行，返回false中止执行
	 * 这里可以加入登录校验、权限拦截等
	 */
	@Override
	Public boolean preHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler) throws Exception {
		// **TODO** Auto-generated method stub
		Return false;
	}
	/**
	 * controller执行后但未返回视图前调用此方法
	 * 这里可在返回用户前对模型数据进行加工处理，比如这里加入公用信息以便页面显示
	 */
	@Override
	Public void postHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		// **TODO** Auto-generated method stub
		
	}
	/**
	 * controller执行后且视图返回后调用此方法
	 * 这里可得到执行controller时的异常信息
	 * 这里可记录操作日志，资源清理等
	 */
	@Override
	Public void afterCompletion(HttpServletRequest request,
			HttpServletResponse response, Object handler, Exception ex)
			throws Exception {
		// TODO Auto-generated method stub
		
	}
}

 
```

 

### 拦截器配置

#### 针对某种mapping配置拦截器

```xml
<bean

	class=*"org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"*>

	<property name=*"interceptors"*>

		<list>

			<ref bean=*"handlerInterceptor1"*/>

			<ref bean=*"handlerInterceptor2"*/>

		</list>

	</property>

</bean>

	<bean id=*"handlerInterceptor1"* class=*"springmvc.intercapter.HandlerInterceptor1"*/>

	<bean id=*"handlerInterceptor2"* class=*"springmvc.intercapter.HandlerInterceptor2"*/>

 
```

 

#### 针对所有mapping配置全局拦截器

```xml
<!--拦截器 -->

<mvc:interceptors>

	<!--多个拦截器,顺序执行 -->

	<mvc:interceptor>

		<mvc:mapping path=*"/**"*/>

		<bean class=*"cn.itcast.springmvc.filter.HandlerInterceptor1"*></bean>

	</mvc:interceptor>

	<mvc:interceptor>

		<mvc:mapping path=*"/**"*/>

		<bean class=*"cn.itcast.springmvc.filter.HandlerInterceptor2"*></bean>

	</mvc:interceptor>

</mvc:interceptors>
```

 

### 正常流程测试

### 代码：

定义两个拦截器分别为：HandlerInterceptor1和HandlerInteptor2，每个拦截器的preHandler方法都返回true。

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5472\wps6.jpg) 

#### 运行流程

HandlerInterceptor1..preHandle..

HandlerInterceptor2..preHandle..

HandlerInterceptor2..postHandle..

HandlerInterceptor1..postHandle..

HandlerInterceptor2..afterCompletion..

HandlerInterceptor1..afterCompletion.

## 中断流程测试

### 代码：

定义两个拦截器分别为：HandlerInterceptor1和HandlerInteptor2。

### 运行流程

HandlerInterceptor1的preHandler方法返回false，HandlerInterceptor2返回true，运行流程如下：

HandlerInterceptor1..preHandle..

从日志看出第一个拦截器的preHandler方法返回false后第一个拦截器只执行了preHandler方法，其它两个方法没有执行，第二个拦截器的所有方法不执行，且controller也不执行了。

HandlerInterceptor1的preHandler方法返回true，HandlerInterceptor2返回false，运行流程如下：

HandlerInterceptor1..preHandle..

HandlerInterceptor2..preHandle..

HandlerInterceptor1..afterCompletion..

从日志看出第二个拦截器的preHandler方法返回false后第一个拦截器的postHandler没有执行，第二个拦截器的postHandler和afterCompletion没有执行，且controller也不执行了。

总结：

preHandle按拦截器定义顺序调用

postHandler按拦截器定义逆序调用

afterCompletion按拦截器定义逆序调用

postHandler在拦截器链内所有拦截器返成功调用

afterCompletion只有preHandle返回true才调用

### 拦截器应用

 

#### 用户身份认证

```java
Public class LoginInterceptor implements HandlerInterceptor{
	@Override
	Public boolean preHandle(HttpServletRequest request,
			HttpServletResponse response, Object handler) throws Exception {
		//如果是登录页面则放行
		if(request.getRequestURI().indexOf("login.action")>=0){
			return true;
		}
		HttpSession session = request.getSession();
		//如果用户已登录也放行
		if(session.getAttribute("user")!=**null**){
			return true;
		}
		//用户没有登录挑战到登录页面
		request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);		
		return false;
}

}

 
```



#### 用户登陆controller

```java
//登陆页面

	@RequestMapping("/login")
	public String login(Model model)throws Exception{
		return "login";
	}

	//登陆提交
	//userid：用户账号，pwd：密码
	@RequestMapping("/loginsubmit")
	public String loginsubmit(HttpSession session,String userid,String pwd)**throws** Exception{
		//向session记录用户身份信息
		session.setAttribute("activeUser", userid);
		return "redirect:item/queryItem.action";
	}
	//退出
	@RequestMapping("/logout")

	public String logout(HttpSession session)throws Exception{

		//session过期

		session.invalidate();

		return "redirect:item/queryItem.action";
	}
```

