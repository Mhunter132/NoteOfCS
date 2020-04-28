## **1.** **概念**

​	身份认证，就是判断一个用户是否为合法用户的处理过程。如声纹，指纹，虹膜等。

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml10584\wps1.png)

## **2.关键对象**

​	上边的流程图中需要理解以下关键对象：

**Subject：主体**

​	访问系统的用户，主体可以是用户、程序等，进行认证的都称为主体；

**Principal：身份信息**

​	是主体（subject）进行身份认证的标识，标识必须具有唯一性，如用户名、手机号、邮箱地址等，一个主体可以有多个身份，但是必须有一个主身份（Primary Principal）。**credential：凭证信息**

​	是只有主体自己知道的安全信息，如密码、证书等。

##  **3.授权概念**

​	授权，即访问控制，控制谁能访问哪些资源。subject进行身份认证后需要分配权限方可访问系统的资源，对于某些资源没有权限是无法访问的

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml10584\wps2.png)

## **4.权限管理解决方案**

#### **4.1什么是粗颗粒度和细颗粒度**

​	对资源类型的管理称为粗颗粒度权限管理，即只控制到菜单、按钮、方法，粗粒度的例子比如：用户具有用户管理的权限，具有导出订单明细的权限。对资源实例的控制称为细颗粒度权限管理，即控制到数据级别的权限，比如：用户只允许修改本部门的员工信息，用户只允许导出自己创建的订单明细。

#### **4.2如何实现粗颗粒度和细颗粒度**

​	对于粗颗粒度的权限管理可以很容易做系统架构级别的功能，即系统功能操作使用统一的粗颗粒度的权限管理。

​	对于细颗粒度的权限管理不建议做成系统架构级别的功能，因为对数据级别的控制是系统的业务需求，随着业务需求的变更业务功能变化的可能性很大，建议对数据级别的权限控制在业务层个性化开发，比如：用户只允许修改自己创建的商品信息可以在service接口添加校验实现，service接口需要传入当前操作人的标识，与商品信息创建人标识对比，不一致则不允许修改商品信息。 

## **5.shiro**

### **5.1.SecurityManager** 

​	SecurityManager即安全管理器，对全部的subject进行安全管理，它是shiro的核心，负责对所有的subject进行安全管理。通过SecurityManager可以完成subject的认证、授权等，实质上SecurityManager是通过Authenticator进行认证，通过Authorizer进行授权，通过SessionManager进行会话管理等。

​	SecurityManager是一个接口，继承了Authenticator, Authorizer, SessionManager这三个接口。

### **5.2. Authenticator**

​	Authenticator即认证器，对用户身份进行认证，Authenticator是一个接口，shiro提供ModularRealmAuthenticator实现类，通过ModularRealmAuthenticator基本上可以满足大多数需求，也可以自定义认证器。

### **5.3.Authorizer**

​	Authorizer即授权器，用户通过认证器认证通过，在访问功能时需要通过授权器判断用户是否有此功能的操作权限。

### **5.4. realm**

​	Realm即领域，相当于datasource数据源，securityManager进行安全认证需要通过Realm获取用户权限数据，比如：如果用户身份数据在数据库那么realm就需要从数据库获取用户身份信息。

​	注意：不要把realm理解成只是从数据源取数据，在realm中还有认证授权校验的相关的代码。

### 	**5.5.essionManager**

​	sessionManager即会话管理，shiro框架定义了一套会话管理，它不依赖web容器的session，所以shiro可以使用在非web应用上，也可以将分布式应用的会话集中在一点管理，此特性可使它实现单点登录。

### 	**5.6.SessionDAO**

​	SessionDAO即会话dao，是对session会话操作的一套接口，比如要将session存储到数据库，可以通过jdbc将会话存储到数据库

### 	**5.7.CacheManager**

​	CacheManager即缓存管理，将用户权限数据存储在缓存，这样可以提高性能。

### 	**5.8. Cryptography**

​	Cryptography即密码管理，shiro提供了一套加密/解密的组件，方便开发。比如提供常用的散列、加/解密等功能。

## **6.认证代码**

​	

```java
@Test
	public void testLoginLogout() {

		// 构建SecurityManager工厂，IniSecurityManagerFactory可以从ini文件中初始化SecurityManager环境
		Factory<SecurityManager> factory = new IniSecurityManagerFactory(
				"classpath:shiro.ini");
 
		// 通过工厂创建SecurityManager
		SecurityManager securityManager = factory.getInstance();
		
		// 将securityManager设置到运行环境中
		SecurityUtils.setSecurityManager(securityManager);

		// 创建一个Subject实例，该实例认证要使用上边创建的securityManager进行
		Subject subject = SecurityUtils.getSubject();

		// 创建token令牌，记录用户认证的身份和凭证即账号和密码 
		UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");

		try {
			// 用户登陆
			subject.login(token);
		} catch (AuthenticationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		// 用户认证状态

		Boolean isAuthenticated = subject.isAuthenticated();

		System.out.println("用户认证状态：" + isAuthenticated);

		// 用户退出

		subject.logout();

		isAuthenticated = subject.isAuthenticated();

		System.out.println("用户认证状态：" + isAuthenticated);

	}
//工厂--->SecurityManager-->设置上下文--->获得主体
```

### **6.1认证执行流程**

​	6.1.1、 创建token令牌，token中有用户提交的认证信息即账号和密码

​	6.1.2、 执行subject.login(token)，最终由securityManager通过Authenticator进行认证

​	6.1.3、 Authenticator的实现ModularRealmAuthenticator调用realm从ini配置文件取用户真实的账号和密码，这里使用的是IniRealm（shiro自带）

​	6.1.4、 IniRealm先根据token中的账号去ini文件中找该账号，如果找不到则给ModularRealmAuthenticator返回null，如果找到则匹配密码，匹配密码成功则认证通过。

### **6.2常见的异常**

**UnknownAccountException**

账号不存在异常如下：

org.apache.shiro.authc.UnknownAccountException: No account found for user。。。。

**IncorrectCredentialsException**

当输入密码错误会抛此异常，如下：

org.apache.shiro.authc.IncorrectCredentialsException: Submitted credentials for token [org.apache.shiro.authc.UsernamePasswordToken - zhangsan, rememberMe=false] did not match the expected credentials.

更多如下：

DisabledAccountException（帐号被禁用）

LockedAccountException（帐号被锁定）

ExcessiveAttemptsException（登录失败次数过多）

ExpiredCredentialsException（凭证过期）等

### **6.3 自定义Realm**

```java
public class CustomRealm1 extends AuthorizingRealm {

	@Override
	public String getName() {
		return "customRealm1";
	}

	//支持UsernamePasswordToken
	@Override
	public boolean supports(AuthenticationToken token) {
		return token instanceof UsernamePasswordToken;
	}

	//认证
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(   <-----------
			AuthenticationToken token) throws AuthenticationException {
		
		//从token中 获取用户身份信息
		String username = (String) token.getPrincipal();
		//拿username从数据库中查询
		//....
		//如果查询不到则返回null
		if(!username.equals("zhang")){//这里模拟查询不到
			return null;
		}
		
		//获取从数据库查询出来的用户密码 
		String password = "123";//这里使用静态数据模拟。。
		
		//返回认证信息由父类AuthenticatingRealm进行认证
		SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
				username, password, getName());

		return simpleAuthenticationInfo;
	}

	//授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(  <---------------------	
			PrincipalCollection principals) {
		// TODO Auto-generated method stub
		return null;
	}

}
---------------------------------------------------------------------------------------------------
    [main]
#自定义 realm
customRealm=cn.itcast.shiro.authentication.realm.CustomRealm1
#将realm设置到securityManager
securityManager.realms=$customRealm
```

## **7 散列算法**

​	散列算法一般用于生成一段文本的摘要信息，散列算法不可逆，将内容可以生成摘要，无法将摘要转成原始内容。散列算法常用于对密码进行散列，常用的散列算法有MD5、SHA。

一般散列算法需要提供一个salt（盐）与原始内容生成摘要信息，这样做的目的是为了安全性，比如：111111的md5值是：96e79218965eb72c92a549dd5a330112，拿着“96e79218965eb72c92a549dd5a330112”去md5破解网站很容易进行破解，如果要是对111111和salt（盐，一个随机数）进行散列，这样虽然密码都是111111加不同的盐会生成不同的散列值。

### **7.1自定义realm**

```java

public class CustomRealm1 extends AuthorizingRealm {

	@Override
	public String getName() {
		return "customRealm1";
	}

	//支持UsernamePasswordToken
	@Override
	public boolean supports(AuthenticationToken token) {
		return token instanceof UsernamePasswordToken;
	}

	//认证
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(
			AuthenticationToken token) throws AuthenticationException {
		
		//用户账号
		String username = (String) token.getPrincipal();
		//根据用户账号从数据库取出盐和加密后的值
		//..这里使用静态数据
		//如果根据账号没有找到用户信息则返回null，shiro抛出异常“账号不存在”
		
		//按照固定规则加密码结果 ，此密码 要在数据库存储，原始密码 是111111，盐是eteokues
		String password = "cb571f7bd7a6f73ab004a70322b963d5";
		//盐，随机数，此随机数也在数据库存储
		String salt = "eteokues";
		
		//返回认证信息
		SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
				username, password, ByteSource.Util.bytes(salt),getName());
			return simpleAuthenticationInfo;
	}
	//授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		// TODO Auto-generated method stub
		return null;
	}
}
--------------------------
    // 授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		// 获取身份信息
		String username = (String) principals.getPrimaryPrincipal();
		// 根据身份信息从数据库中查询权限数据
		//....这里使用静态数据模拟
		List<String> permissions = new ArrayList<String>();
		permissions.add("user:create");
		permissions.add("user.delete");
		
		//将权限信息封闭为AuthorizationInfo
		
		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		for(String permission:permissions){
			simpleAuthorizationInfo.addStringPermission(permission);
		}
		
		return simpleAuthorizationInfo;
	}
--------------------------------------ini文件配置--------------------------------------------------
    [main]
#定义凭证匹配器
credentialsMatcher=org.apache.shiro.authc.credential.HashedCredentialsMatcher
#散列算法
credentialsMatcher.hashAlgorithmName=md5
#散列次数
credentialsMatcher.hashIterations=1

#将凭证匹配器设置到realm
customRealm=cn.itcast.shiro.authentication.realm.CustomRealm2
customRealm.credentialsMatcher=$credentialsMatcher
securityManager.realms=$customRealm
```

## **8.shiro授权**

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml4376\wps1.png)

### **8.1授权方式**

Shiro 支持三种方式的授权：

**编程式**：通过写if/else 授权代码块完成：

Subject subject = SecurityUtils.getSubject();

if(subject.hasRole(“admin”)) {

//有权限

} else {

//无权限

}

**注解式**：通过在执行的Java方法上放置相应的注解完成：

@RequiresRoles("admin")

public void hello() {

//有权限

}

**JSP/GSP 标签**：在JSP/GSP 页面通过相应的标签完成：

< shiro:hasRole name="admin">

< !— 有权限— >

< /shiro:hasRole >

### **8.2授权测试**

```ini
[users]
#用户zhang的密码是123，此用户具有role1和role2两个角色
zhang=123,role1,role2
wang=123,role2

[roles]
#角色role1对资源user拥有create、update权限
role1=user:create,user:update  #这行是权限字符串
#角色role2对资源user拥有create、delete权限
role2=user:create,user:delete
#角色role3对资源user拥有create权限
role3=user:create
```

### **8.3测试代码**

```java
public void testPermission() {

		// 从ini文件中创建SecurityManager工厂
		Factory<SecurityManager> factory = new IniSecurityManagerFactory(
				"classpath:shiro-permission.ini");

		// 创建SecurityManager
		SecurityManager securityManager = factory.getInstance();

		// 将securityManager设置到运行环境
		SecurityUtils.setSecurityManager(securityManager);

		// 创建主体对象
		Subject subject = SecurityUtils.getSubject();

		// 对主体对象进行认证
		// 用户登陆
		// 设置用户认证的身份(principals)和凭证(credentials)
		UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
		try {
			subject.login(token);
		} catch (AuthenticationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		// 用户认证状态
		Boolean isAuthenticated = subject.isAuthenticated();

		System.out.println("用户认证状态：" + isAuthenticated);

		// 用户授权检测 基于角色授权
		// 是否有某一个角色
		System.out.println("用户是否拥有一个角色：" + subject.hasRole("role1"));
		// 是否有多个角色
		System.out.println("用户是否拥有多个角色：" + subject.hasAllRoles(Arrays.asList("role1", "role2")));
		
//		subject.checkRole("role1");
//		subject.checkRoles(Arrays.asList("role1", "role2"));

		// 授权检测，失败则抛出异常
		// subject.checkRole("role22");

		// 基于资源授权
		System.out.println("是否拥有某一个权限：" + subject.isPermitted("user:delete"));
		System.out.println("是否拥有多个权限：" + subject.isPermittedAll("user:create:1",	"user:delete"));
		
		//检查权限
		subject.checkPermission("sys:user:delete");
		subject.checkPermissions("user:create:1","user:delete");
}
-------------------------------------基于角色的授权-------------------------------------------------
// 用户授权检测 基于角色授权
		// 是否有某一个角色
		System.out.println("用户是否拥有一个角色：" + subject.hasRole("role1"));
		// 是否有多个角色
		System.out.println("用户是否拥有多个角色：" + subject.hasAllRoles(Arrays.asList("role1", "role2")));
		

对应的check方法：
subject.checkRole("role1");
subject.checkRoles(Arrays.asList("role1", "role2"));

上边check方法如果授权失败则抛出异常：
org.apache.shiro.authz.UnauthorizedException: Subject does not have role [.....]
-----------------------------------基于资源授权-----------------------------------------------------
    // 基于资源授权
		System.out.println("是否拥有某一个权限：" + subject.isPermitted("user:delete"));
		System.out.println("是否拥有多个权限：" + subject.isPermittedAll("user:create:1",	"user:delete"));
对应的check方法：
subject.checkPermission("sys:user:delete");
subject.checkPermissions("user:create:1","user:delete");
```

### **8.4shiro-realm代码**

ini配置文件不变

继承AuthorizingRealm ,并重写doGetAuthenticationInfo，doGetAuthorizationInfo

```java
public class CustomRealm2 extends AuthorizingRealm {

	@Override
	public String getName() {
		return "customRealm1";
	}

	//支持UsernamePasswordToken
	@Override
	public boolean supports(AuthenticationToken token) {
		return token instanceof UsernamePasswordToken;
	}

	//认证
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(
			AuthenticationToken token) throws AuthenticationException {
		
		//用户账号
		String username = (String) token.getPrincipal();
		//根据用户账号从数据库取出盐和加密后的值
		//..这里使用静态数据
		//如果根据账号没有找到用户信息则返回null，shiro抛出异常“账号不存在”
		
		//按照固定规则加密码结果 ，此密码 要在数据库存储，原始密码 是111111，盐是eteokues
		String password = "cb571f7bd7a6f73ab004a70322b963d5";
		//盐，随机数，此随机数也在数据库存储
		String salt = "eteokues";
		
		//返回认证信息
		SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
				username, password, ByteSource.Util.bytes(salt),getName());
			return simpleAuthenticationInfo;
	}
	//授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		// 获取身份信息
		String username = (String) principals.getPrimaryPrincipal();
		// 根据身份信息从数据库中查询权限数据
		//....这里使用静态数据模拟
		List<String> permissions = new ArrayList<String>();
		permissions.add("user:create");
		permissions.add("user.delete");
		
		//将权限信息封闭为AuthorizationInfo
		
		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		for(String permission:permissions){
			simpleAuthorizationInfo.addStringPermission(permission);
		}
		
		return simpleAuthorizationInfo;
	}
	
}
--------------------------
    // 授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		// 获取身份信息
		String username = (String) principals.getPrimaryPrincipal();
		// 根据身份信息从数据库中查询权限数据
		//....这里使用静态数据模拟
		List<String> permissions = new ArrayList<String>();
		permissions.add("user:create");
		permissions.add("user.delete");
		
		//将权限信息封闭为AuthorizationInfo
		
		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		for(String permission:permissions){
			simpleAuthorizationInfo.addStringPermission(permission);
		}
		
		return simpleAuthorizationInfo;
	}
```

### **8.5授权执行流程**

1、 执行subject.isPermitted("user:create")

2、 securityManager通过ModularRealmAuthorizer进行授权

3、 ModularRealmAuthorizer调用realm获取权限信息

4、 ModularRealmAuthorizer再通过permissionResolver解析权限字符串，校验是否匹配

## **9.与ssm项目继承**

```xml
shiro与spring web项目整合

	shiro与springweb项目整合在“基于url拦截实现的工程”基础上整合，基于url拦截实现的工程的技术架构是springmvc+mybatis，整合注意两点：
	1、shiro与spring整合
	2、加入shiro对web应用的支持

9.1.1取消原springmvc认证和授权拦截器
去掉springmvc.xml中配置的LoginInterceptor和PermissionInterceptor拦截器。

9.1.2加入shiro的 jar包
shiro-spring.jar
shiro-web.jar
shiro-core.jar

9.1.3web.xml添加shiro Filter

<!-- shiro过虑器，DelegatingFilterProx会从spring容器中找shiroFilter -->
	<filter>
	<filter-name>shiroFilter</filter-name>
	<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
		<init-param>
			<param-name>targetFilterLifecycle</param-name>
			<param-value>true</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>shiroFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>


9.1.4applicationContext-shiro.xml 

<!-- Shiro 的Web过滤器 -->
	<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
		<property name="securityManager" ref="securityManager" />
		<!-- 如果没有认证将要跳转的登陆地址，http可访问的url，如果不在表单认证过虑器FormAuthenticationFilter中指定此地址就为身份认证地址 -->
		<property name="loginUrl" value="/login.action" />
<!-- 没有权限跳转的地址 -->
		<property name="unauthorizedUrl" value="/refuse.jsp" />
<!-- shiro拦截器配置 -->
		<property name="filters">
			<map>
				<entry key="authc" value-ref="formAuthenticationFilter" />
			</map>
		</property>
		<property name="filterChainDefinitions">
			<value>
				<!-- 必须通过身份认证方可访问，身份认 证的url必须和过虑器中指定的loginUrl一致 -->
				/loginsubmit.action = authc
				<!-- 退出拦截，请求logout.action执行退出操作 -->
				/logout.action = logout
				<!-- 无权访问页面 -->
				/refuse.jsp = anon
				<!-- roles[XX]表示有XX角色才可访问 -->
				/item/list.action = roles[item],authc
				/js/** anon
				/images/** anon
				/styles/** anon
				<!-- user表示身份认证通过或通过记住我认证通过的可以访问 -->
				/** = user
				<!-- /**放在最下边，如果一个url有多个过虑器则多个过虑器中间用逗号分隔，如：/** = user,roles[admin] -->

			</value>
		</property>
	</bean>


	<!-- 安全管理器 -->
	<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
		<property name="realm" ref="userRealm" />
		
	</bean>

	<!-- 自定义 realm -->
	<bean id="userRealm" class="cn.itcast.ssm.realm.CustomRealm1">
        
	</bean>
	<!-- 基于Form表单的身份验证过滤器，不配置将也会注册此过虑器，表单中的用户账号、密码及loginurl将采用默认值，建议配置 -->
	<bean id="formAuthenticationFilter"
		class="org.apache.shiro.web.filter.authc.FormAuthenticationFilter">
		<!-- 表单中账号的input名称 -->
		<property name="usernameParam" value="usercode" />
		<!-- 表单中密码的input名称 -->
		<property name="passwordParam" value="password" />
		<!-- <property name="rememberMeParam" value="rememberMe"/> -->
		<!-- loginurl：用户登陆地址，此地址是可以http访问的url地址 -->
		<property name="loginUrl" value="/loginsubmit.action" />
	</bean>



securityManager：这个属性是必须的。
loginUrl：没有登录认证的用户请求将跳转到此地址，不是必须的属性，不输入地址的话会自动寻找项目web项目的根目录下的”/login.jsp”页面。
unauthorizedUrl：没有权限默认跳转的页面。
```

## **10.使用注解授权**

在springmvc.xml中配置shiro注解支持，可在controller方法中使用shiro注解配置权限：

```xml
<!-- 开启aop，对类代理 -->
	<aop:config proxy-target-class="true"></aop:config>
	<!-- 开启shiro注解支持 -->
	<bean
		class="
			org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
		<property name="securityManager" ref="securityManager" />
	</bean>
	修改Controller代码，在方法上添加授权注解，如下：

```

修改Controller代码，在方法上添加授权注解，如下：

```java
// 查询商品列表
	@RequestMapping("/queryItem")
	@RequiresPermissions("item:query")
	public ModelAndView queryItem() throws Exception {
```

上边代码@RequiresPermissions("item:query")表示必须拥有“item:query”权限方可执行。

其它的方法参考示例添加注解，一边添加一边思考这比基于url拦截有什么好处。

###  **10.1自定义realm**

```java
public class CustomRealm1 extends AuthorizingRealm {

	@Autowired
	private SysService sysService;

	@Override
	public String getName() {
		return "customRealm";
	}

	// 支持什么类型的token
	@Override
	public boolean supports(AuthenticationToken token) {
		return token instanceof UsernamePasswordToken;
	}

	// 认证
	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(
			AuthenticationToken token) throws AuthenticationException {

		// 从token中 获取用户身份信息
		String username = (String) token.getPrincipal();
		// 拿username从数据库中查询
		// ....
		// 如果查询不到则返回null
		if (!username.equals("zhang")) {// 这里模拟查询不到
			return null;
		}

		// 获取从数据库查询出来的用户密码
		String password = "123";// 这里使用静态数据模拟。。
		
		// 根据用户id从数据库取出菜单
		//...先用静态数据
		List<SysPermission> menus = new ArrayList<SysPermission>();;
		
		SysPermission sysPermission_1 = new SysPermission();
		sysPermission_1.setName("商品管理");
		sysPermission_1.setUrl("/item/queryItem.action");
		SysPermission sysPermission_2 = new SysPermission();
		sysPermission_2.setName("用户管理");
		sysPermission_2.setUrl("/user/query.action");
		
		menus.add(sysPermission_1);
		menus.add(sysPermission_2);
		
		// 构建用户身体份信息
		ActiveUser activeUser = new ActiveUser();
		activeUser.setUserid(username);
		activeUser.setUsername(username);
		activeUser.setUsercode(username);
		activeUser.setMenus(menus);

		// 返回认证信息由父类AuthenticatingRealm进行认证
		SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
				activeUser, password, getName());

		return simpleAuthenticationInfo;
	}

	// 授权
	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		// 获取身份信息
		ActiveUser activeUser = (ActiveUser) principals.getPrimaryPrincipal();
		//用户id
		String userid = activeUser.getUserid();
		// 根据用户id从数据库中查询权限数据
		// ....这里使用静态数据模拟
		List<String> permissions = new ArrayList<String>();
		permissions.add("item:query");
		permissions.add("item:update");

		// 将权限信息封闭为AuthorizationInfo

		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		for (String permission : permissions) {
			simpleAuthorizationInfo.addStringPermission(permission);
		}

		return simpleAuthorizationInfo;
	}

}
```

### **10.2登录**

```java
//用户登陆页面
	@RequestMapping("/login")
	public String login()throws Exception{
		return "login";
	}
	// 用户登陆提交
	@RequestMapping("/loginsubmit")
	public String loginsubmit(Model model, HttpServletRequest request)
			throws Exception {

		// shiro在认证过程中出现错误后将异常类路径通过request返回
		String exceptionClassName = (String) request
				.getAttribute("shiroLoginFailure");
		if (UnknownAccountException.class.getName().equals(exceptionClassName)) {
			throw new CustomException("账号不存在");
		} else if (IncorrectCredentialsException.class.getName().equals(
				exceptionClassName)) {
			throw new CustomException("用户名/密码错误");
		} else{
			throw new Exception();//最终在异常处理器生成未知错误
		}
	}
```

### **10.3首页**

```java
由于session由shiro管理，需要修改首页的controller方法：

//系统首页
	@RequestMapping("/first")
	public String first(Model model)throws Exception{
		
		//主体
		Subject subject = SecurityUtils.getSubject();
		//身份
		ActiveUser activeUser = (ActiveUser) subject.getPrincipal();
		model.addAttribute("activeUser", activeUser);
		return "/first";
}
```

### **10.4退出**

```xml
由于使用shiro的sessionManager，不用开发退出功能，使用shiro的logout拦截器即可。

<!-- 退出拦截，请求logout.action执行退出操作 -->
/logout.action = logout
```

### **10.5无权限**

```
当用户无操作权限，shiro将跳转到refuse.jsp页面。
参考：applicationContext-shiro.xml
```

## **11.实现连接数据库**

```xml
添加凭证匹配器实现md5加密校验。
修改applicationContext-shiro.xml：

<!-- 凭证匹配器 -->
	<bean id="credentialsMatcher"
		class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
		<property name="hashAlgorithmName" value="md5" />
		<property name="hashIterations" value="1" />
	</bean>

<!-- 自定义 realm -->
	<bean id="userRealm" class="cn.itcast.ssm.realm.CustomRealm1">
		<property name="credentialsMatcher" ref="credentialsMatcher" />
	</bean>
```

### **11.1自定义realm**

```java
//修改realm代码从数据库中查询用户身份信息和权限信息，将sysService注入realm。

public class CustomRealm1 extends AuthorizingRealm {

	@Autowired
	private SysService sysService;

	@Override
	public String getName() {
		return "customRealm";
	}

	// 支持什么类型的token
	@Override
	public boolean supports(AuthenticationToken token) {
		return token instanceof UsernamePasswordToken;
	}

	@Override
	protected AuthenticationInfo doGetAuthenticationInfo(
			AuthenticationToken token) throws AuthenticationException {
		// 从token中获取用户身份
		String usercode = (String) token.getPrincipal();

		SysUser sysUser = null;
		try {
			sysUser = sysService.findSysuserByUsercode(usercode);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		// 如果账号不存在
		if (sysUser == null) {
			throw new UnknownAccountException("账号找不到");
		}

		// 根据用户id取出菜单
		List<SysPermission> menus = null;
		try {
			menus = sysService.findMenuList(sysUser.getId());
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		// 用户密码
		String password = sysUser.getPassword();
		//盐
		String salt = sysUser.getSalt();
		
		// 构建用户身体份信息
		ActiveUser activeUser = new ActiveUser();
		activeUser.setUserid(sysUser.getId());
		activeUser.setUsername(sysUser.getUsername());
		activeUser.setUsercode(sysUser.getUsercode());
		activeUser.setMenus(menus);
		
		SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(
				activeUser, password, ByteSource.Util.bytes(salt),getName());
		
		return simpleAuthenticationInfo;
	}

	@Override
	protected AuthorizationInfo doGetAuthorizationInfo(
			PrincipalCollection principals) {
		
		//身份信息
		ActiveUser activeUser = (ActiveUser) principals.getPrimaryPrincipal();
		//用户id
		String userid = activeUser.getUserid();
		//获取用户权限
		List<SysPermission> permissions = null;
		try {
			permissions = sysService.findSysPermissionList(userid);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		//构建shiro授权信息
		SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
		for(SysPermission sysPermission:permissions){
			simpleAuthorizationInfo.addStringPermission(sysPermission.getPercode());
		}
		
		return simpleAuthorizationInfo;
		
	}

}
```

## **12.缓存**

```xml
12.1缓存
	shiro每个授权都会通过realm获取权限信息，为了提高访问速度需要添加缓存，第一次从realm中读取权限数据，之后不再读取，这里Shiro和Ehcache整合。

12.2添加Ehcache的jar包
ehecache-core.jar
ehcache-shiro.jar

12.3配置
在applicationContext-shiro.xml中配置缓存管理器。
<!-- 安全管理器 -->
	<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
		<property name="realm" ref="userRealm" />
		<property name="sessionManager" ref="sessionManager" />
		<property name="cacheManager" ref="cacheManager"/>
	</bean>

<!-- 缓存管理器 -->
    <bean id="cacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
    </bean>
```

## **13.session管理**

```xml
在applicationContext-shiro.xml中配置sessionManager：
<!-- 安全管理器 -->
	<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
		<property name="realm" ref="userRealm" />
		<property name="sessionManager" ref="sessionManager" />
	</bean>
<!-- 会话管理器 -->
    <bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
        <!-- session的失效时长，单位毫秒 -->
        <property name="globalSessionTimeout" value="600000"/>
        <!-- 删除失效的session -->
        <property name="deleteInvalidSessions" value="true"/>
    </bean>
```

## **14.验证码**

```java
//自定义FormAuthenticationFilter
//需要在验证账号和名称之前校验验证码。

public class MyFormAuthenticationFilter extends FormAuthenticationFilter {
	protected boolean onAccessDenied(ServletRequest request,
			ServletResponse response, Object mappedValue) throws Exception {

		// 校验验证码
		// 从session获取正确的验证码
		HttpSession session = ((HttpServletRequest)request).getSession();
		//页面输入的验证码
		String randomcode = request.getParameter("randomcode");
		//从session中取出验证码
		String validateCode = (String) session.getAttribute("validateCode");
		if (!randomcode.equals(validateCode)) {
			// randomCodeError表示验证码错误 
			request.setAttribute("shiroLoginFailure", "randomCodeError");
			//拒绝访问，不再校验账号和密码 
			return true;
		}
		
		return super.onAccessDenied(request, response, mappedValue);
	}
}
```

### **14.1 修改FormAuthenticationFilter配置**

```xml
修改applicationContext-shiro.xml中对FormAuthenticationFilter的配置。
<bean id="formAuthenticationFilter"
		class="org.apache.shiro.web.filter.authc.FormAuthenticationFilter">
改为		
<bean id="formAuthenticationFilter"
		class="cn.itcast.ssm.shiro.MyFormAuthenticationFilter">
```

### **14.2登录页面**

```xml
添加验证码：
<TR>
	<TD>验证码：</TD>
	<TD><input id="randomcode" name="randomcode" size="8" /> <img
		id="randomcode_img" src="${baseurl}validatecode.jsp" alt=""
		width="56" height="20" align='absMiddle' /> <a
		href=javascript:randomcode_refresh()>刷新</a></TD>
</TR>
```

### **14.3配置validatecode.jsp匿名访问**

修改applicationContext-shiro.xml：

![1563867672379](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1563867672379.png)

## **15记住我**

​	用户登陆选择“自动登陆”本次登陆成功会向cookie写身份信息，下次登陆从cookie中取出身份信息实现自动登陆

### **15.1需要实现序列化接口**

向cookie记录身份信息需要用户身份信息对象实现序列化接口，如下：

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps3.jpg)![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps4.jpg) 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps5.jpg) 

### **15.2配置**

```xml
<!-- 安全管理器 -->
<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
		<property name="realm" ref="userRealm" />
		<property name="sessionManager" ref="sessionManager" />
		<property name="cacheManager" ref="cacheManager"/>
		<!-- 记住我 -->
		<property name="rememberMeManager" ref="rememberMeManager"/>
</bean>

<!-- rememberMeManager管理器 -->
<bean id="rememberMeManager" class="org.apache.shiro.web.mgt.CookieRememberMeManager">
		<property name="cookie" ref="rememberMeCookie" />
</bean>
<!-- 记住我cookie -->
<bean id="rememberMeCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
		<constructor-arg value="rememberMe" />
		<!-- 记住我cookie生效时间30天 -->
		<property name="maxAge" value="2592000" />
</bean>


<!--修改formAuthenticationFitler添加页面中“记住我checkbox”的input名称：-->
<bean id="formAuthenticationFilter"
		class="cn.itcast.ssm.shiro.MyFormAuthenticationFilter">
		<!-- 表单中账号的input名称 -->
		<property name="usernameParam" value="usercode" />
		<!-- 表单中密码的input名称 -->
		<property name="passwordParam" value="password" />
		<property name="rememberMeParam" value="rememberMe"/>
		<!-- loginurl：用户登陆地址，此地址是可以http访问的url地址 -->
		<property name="loginUrl" value="/loginsubmit.action" />
</bean>
```

```jsp
在login.jsp中添加“记住我”checkbox。

<TR>
	<TD></TD>
	<TD>
		<input type="checkbox" name="rememberMe" />自动登陆
	</TD>
</TR>
```

