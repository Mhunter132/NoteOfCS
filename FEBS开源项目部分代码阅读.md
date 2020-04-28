# FEBS开源项目部分源码阅读笔记

### controller

```java
//为了简化篇幅删除导入的包

@Controller
public class DeptController {
    //日志log
    private Logger log = LoggerFactory.getLogger(this.getClass());

    @Autowired
    private DeptService deptService;

	//自定义的log日志注解，并由AOP实现日志的输出到文件和控制台
    //可以记录用户操作行为
    @Log("获取部门信息")
    @RequestMapping("dept")
    //shiro权限校验（获取部门员工list），控制权限粒度在按钮和功能级别
    @RequiresPermissions("dept:list")
    public String index() {
        return "system/dept/dept";
    }

		// 删除功能相近的crud代码
}
```

### aspect

```java
//为了简化篇幅删除导入的包

/**
 * AOP 记录用户操作日志
 *
 * @author shw
 * @link https://mrbird.cc/Spring-Boot-AOP%20log.html
 */
//切面
@Aspect
@Component
public class LogAspect {

    @Autowired
    private FebsProperties febsProperties;

    @Autowired
    private LogService logService;

	//切入点 ，切入点是@log注解
    @Pointcut("@annotation(cc.mrbird.common.annotation.Log)")
    public void pointcut() {
        // do nothing
    }
	//织入部分
    @Around("pointcut()") // 环绕通知
    public Object around(ProceedingJoinPoint point) throws Throwable {
        Object result = null;
        //java 新api LocalDate、LocalTime、LocalDateTime【复习】
        long beginTime = System.currentTimeMillis();
        // 执行方法 ,执行业务逻辑方法
        result = point.proceed();
        // 获取request
        //这个工具类使用的spring框架的RequestContextHolder 获取请求参数->强转（ServletRequestAttributes）-> get到HttpServletRequest
        HttpServletRequest request = HttpContextUtils.getHttpServletRequest();
        // 设置IP地址
        String ip = IPUtils.getIpAddr(request);
        // 执行时长(毫秒)
        long time = System.currentTimeMillis() - beginTime;
        //febs 的是否开启log日志
        if (febsProperties.isOpenAopLog()) {
            // 保存日志
            //shiro提供的 工具类，获取用户的
            User user = (User) SecurityUtils.getSubject().getPrincipal();
            SysLog log = new SysLog();
            log.setUsername(user.getUsername());
            log.setIp(ip);
            log.setTime(time);
            logService.saveLog(point, log);
        }
        return result;
    }
}
```

### service

```java
public interface LogService extends IService<SysLog> {
	
	List<SysLog> findAllLogs(SysLog log);
	
	void deleteLogs(String logIds);
	//多线程异步调用，不具有实时性的功能
	@Async
	void saveLog(ProceedingJoinPoint point, SysLog log) throws JsonProcessingException;
}
```



参考blog:https://www.cnblogs.com/zhengbin/p/6104502.html 
简单介绍：
Spring为任务调度与异步方法执行提供了注解支持。通过在方法上设置@Async注解，可使得方法被异步调用。也就是说调用者会在调用时立即返回，而被调用方法的实际执行是交给Spring的TaskExecutor来完成。
注意：

1. 异步调用，通过开启新的线程来执行调用的方法，不影响主线程。异步方法实际的执行交给了Spring的TaskExecutor来完成。
2. 通过直接获取返回值得方式是不行的，这里就需要用到异步回调，异步方法返回值必须为Future<>，就像Callable与Future。针对有返回值的情况

how to  builde Log  strategy 

日志配置策略:

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false">
    <contextName>这个位置就是你控制台时间后面的名字</contextName>
    <property name="log.path" value="log" />
    <property name="log.maxHistory" value="15" />
    <property name="log.pattern" value="%d{HH:mm:ss.SSS} %contextName [%thread / 顾名思义就是进程] %-5level %logger{36} - %msg%n" />

    <!--输出到控制台-->
    <!-- 一般比较固定-->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
    </appender>

    <!--输出到文件-->
    <appender name="file_info" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/info/info.%d{yyyy-MM-dd}.log</fileNamePattern>
            <MaxHistory>${log.maxHistory}</MaxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>INFO</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <appender name="file_error" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${log.path}/error/error.%d{yyyy-MM-dd}.log</fileNamePattern>
        </rollingPolicy>
        <encoder>
            <pattern>${log.pattern}</pattern>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
    </appender>

    <root level="debug">
        <appender-ref ref="console" />
    </root>

    <root level="info">
        <appender-ref ref="file_info" />
        <appender-ref ref="file_error" />
    </root>
</configuration>
```

### 定任务配置

```java
@Configuration
public class ScheduleConfig {

	@Bean
	public SchedulerFactoryBean schedulerFactoryBean(DataSource dataSource) {
		//定时任务工厂
        SchedulerFactoryBean factory = new SchedulerFactoryBean();
		//设置数据源
        factory.setDataSource(dataSource);
		
		// quartz参数（Map类型）Map<全类名，相应的值>
		Properties prop = new Properties();
		prop.put("org.quartz.scheduler.instanceName", "MyScheduler");
		prop.put("org.quartz.scheduler.instanceId", "AUTO");
		// 线程池配置
		prop.put("org.quartz.threadPool.class", "org.quartz.simpl.SimpleThreadPool");
		prop.put("org.quartz.threadPool.threadCount", "20");
		prop.put("org.quartz.threadPool.threadPriority", "5");
		// JobStore配置
		prop.put("org.quartz.jobStore.class", "org.quartz.impl.jdbcjobstore.JobStoreTX");
		// 集群配置
		prop.put("org.quartz.jobStore.isClustered", "true");
		prop.put("org.quartz.jobStore.clusterCheckinInterval", "15000");
		prop.put("org.quartz.jobStore.maxMisfiresToHandleAtATime", "1");

		prop.put("org.quartz.jobStore.misfireThreshold", "12000");
		prop.put("org.quartz.jobStore.tablePrefix", "QRTZ_");
		//set定时任务值
        factory.setQuartzProperties(prop);
		//定时任务名字
		factory.setSchedulerName("MyScheduler");
		// 延时启动 boolearn
		factory.setStartupDelay(1);
		factory.setApplicationContextSchedulerContextKey("applicationContextKey");
		// 可选，QuartzScheduler
		// 启动时更新己存在的Job，这样就不用每次修改targetObject后删除qrtz_job_details表对应记录了
		factory.setOverwriteExistingJobs(true);
		// 设置自动启动，默认为true
		factory.setAutoStartup(true);

		return factory;
	}
}
```

### 登录

```java
 public ResponseBo login(String username, String password, String code, Boolean rememberMe) {
        if (!StringUtils.isNotBlank(code)) {
            return ResponseBo.warn("验证码不能为空！");
        }
        Session session = super.getSession();
        String sessionCode = (String) session.getAttribute(CODE_KEY);
        if (!code.equalsIgnoreCase(sessionCode)) {
            return ResponseBo.warn("验证码错误！");
        }
        // 密码 MD5 加密
        password = MD5Utils.encrypt(username.toLowerCase(), password);
        UsernamePasswordToken token = new UsernamePasswordToken(username, password, rememberMe);
        try {
            Subject subject = getSubject();
            if (subject != null)
                subject.logout();
            //这一步必须保证subject是空的
            super.login(token);
            this.userService.updateLoginTime(username);
            return ResponseBo.ok();
        } catch (UnknownAccountException | IncorrectCredentialsException | LockedAccountException e) {
            return ResponseBo.error(e.getMessage());
        } catch (AuthenticationException e) {
            return ResponseBo.error("认证失败！");
        }
    }
```

```java
//条件注解 ，true就是执行，false就不执行下面的内容
@ConditionalOnProperty(name = "febs.showsql", havingValue = "true")

```