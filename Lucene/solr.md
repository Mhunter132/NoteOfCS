# solr

## 1.什么是solr

​	Solr可以独立运行，运行在Jetty、Tomcat等这些Servlet容器中，Solr 索引的实现方法很简单，用 **POST 方法**向 Solr 服务器**发送一个描述 Field 及其内容的 XML 文档**，Solr根据xml文档添加、删除、更新索引 

​	Solr 搜索只需要**发送 HTTP GET 请求**，然后对 Solr **返回Xml、json等格式**的查询结果进行解析，组织页面布局。

## 2.安装solr

​	1.solr-4.10.3.zip

​	2.解压solr

```
bin：solr的运行脚本
contrib：solr的一些贡献软件/插件，用于增强solr的功能。
dist：该目录包含build过程中产生的war和jar文件，以及相关的依赖文件。
docs：solr的API文档
example：solr工程的例子目录：
example/solr：
	该目录是一个包含了默认配置信息的Solr的Core目录。
example/multicore：
	该目录包含了在Solr的multicore中设置的多个Core目录。 
example/webapps：
    该目录中包括一个solr.war，该war可作为solr的运行实例工程。
licenses：solr相关的一些许可信息
```

​	3.Solr整合tomcat

​	4.Solr Home与SolrCore

```
创建一个Solr home目录，SolrHome是Solr运行的主目录，目录中包括了运行Solr实例所有的配置文件和数据文件，Solr实例就是SolrCore，一个SolrHome可以包括多个SolrCore（Solr实例），每个SolrCore提供单独的搜索和索引服务。
example\solr是一个solr home目录结构，如下：
	*bin
	*collection1
	*README.TXT
	*solr.xml
	*zoo.cfg

上图中“collection1”是一个SolrCore（Solr实例）目录 ，目录内容如下所示：

	*conf
	*data
	*core.properties
	*README.TXT
说明：
collection1：叫做一个Solr运行实例SolrCore，SolrCore名称不固定，一个solr运行实例对外单独提供索引和搜索接口。
solrHome中可以创建多个solr运行实例SolrCore。
一个solr的运行实例对应一个索引目录。
conf是SolrCore的配置文件目录 。
data目录存放索引文件需要创建
```

整合步骤

​	第一步：安装tomcat。D:\temp\apache-tomcat-7.0.53

​	第二步：把solr的war包复制到tomcat 的webapp目录下。

​	把\solr-4.10.3\dist\solr-4.10.3.war复制到D:\temp\apache-tomcat-7.0.53\webapps下。

​	改名为solr.war
​	第三步：solr.war解压。使用压缩工具解压或者启动tomcat自动解压。解压之后删除solr.war

​	第四步：把\solr-4.10.3\example\lib\ext目录下的所有的jar包添加到solr工程中

​	第五步：配置solrHome和solrCore。

​	1）创建一个solrhome（存放solr所有配置文件的一个文件夹）。\solr-4.10.3\example\solr目录就是一个标准的solrhome。

​	2）把\solr-4.10.3\example\solr文件夹复制到D:\temp\0108路径下，改名为solrhome，改名不是必须的，是为了便于理解。

​	3）在solrhome下有一个文件夹叫做collection1这就是一个solrcore。就是一个solr的实例。一个solrcore相当于mysql中一个数据库。Solrcore之间是相互隔离。

​	i. 在solrcore中有一个文件夹叫做conf，包含了索引solr实例的配置信息。

​	ii. 在conf文件夹下有一个solrconfig.xml。配置实例的相关信息。**如果使用默认配置可以不用做任何修改。**

​	**Xml的配置信息：**

​	**Lib**：solr服务依赖的扩展包，默认的路径是collection1\lib文件夹，如果没有		 就创建一个

​	**dataDir**：配置了索引库的存放路径。默认路径是collection1\data文件夹，如			果data文件夹，会自动创建。

​	**requestHandler：**

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps6.jpg) 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps7.jpg) 

​	第六步：告诉solr服务器配置文件也就是solrHome的位置。修改web.xml使用jndi的方式告诉solr服务器。

​	**Solr/home名称必须是固定的。**

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps8.jpg) 

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml5736\wps9.jpg) 

​	第七步：启动tomcat

​	第八步：访问http://localhost:8080/solr/

## 后台管理

​	1.Dashboard仪表盘

​	2.LoggingSolr运行日志信息

​	3.Core Admin

Solr Core的管理界面。Solr Core 是Solr的一个独立运行实例单位，它可以对外提供索引和搜索服务，一个Solr工程可以运行多个SolrCore（Solr实例），一个Core对应一个索引目录。

​	4.java properties

Solr在JVM 运行环境中的属性信息，包括类路径、文件编码、jvm内存设置等信息。

​	5.Tread Dump

显示Solr Server中当前活跃线程信息，同时也可以跟踪线程运行栈信息。

​	6.Core selector

选择一个SolrCore进行详细操作，如下： 

​	7.Analysis 

以测试索引分析器和搜索分析器的执行情况。

​	8.Dataimport

可以定义数据导入处理器，从关系数据库将数据导入 到Solr索引库中。

 	9.Document

创建索引、更新索引、删除索引等操作，

/update表示更新索引，solr默认根据id（唯一约束）域来更新Document的内容，如果根据id值搜索不到id域则会执行添加操作，如果找到则更新

通过/select执行搜索索引，必须指定“q”查询条件方可搜索。