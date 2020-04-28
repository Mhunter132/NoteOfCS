# bootstrap入门

## 1.1下载源码

官网

## 1.2.目录结构

```txt
1.dist    发布版本
2.docs    文档
3.fonts   字体
4.grunt  js命令行构建工具
5.less    源码
```

## 1.3.内容

![1564152649648](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1564152649648.png)

## 1.4.简洁模板

```html
<！DOCTYPE html> <！-- HTML5约束（固定值） -->
<html lang="zh-CN"><！-- 声明语言，建议编辑-->
<head>
<！--响应式开发必须使用，且必须在<head>前三行-->
	<meta charset="utf-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta name="viewport" content= "width=device-width， initial-scale=1" />
<title>Bootstrap模板</title>
	<！-- Bootstrap 预定义的CsSS样式、jQuery核心类库、Bootstrap 类库-->
	<link href="../ lib/ bootstrap/ css/ bootstrap.min.css" rel="stylesheet">
	<script src="../1ib/ jquery/jquery-1.11.0.js"></script>
	<script src="../1ib/ bootstrap/ js/bootstrap . min. js"></script>
</head>
<body>
	<h1>你好，世界！ </h1>
</body>
</html>
```

## 1.5.完整模板

```
http://v3.bootcss.com/getting- started/#template
```

# 2.首页

```html
● Bootstrap需要为页面内容和栅格系统包裹一个.container容器。提供的两个容器如下：
.container 类用于固定宽度并支持响应式布局的容器。
<div class="container">
</div>
●.containe-fluid 类用于100%宽度，占据全部视口（viewport） 的容器。
<div class="container- fluid">
</div>
● 例如：
<！--居中显示，两边有留白-->
<div class="container" style="border：1px solid #f00； height：100px；"></div>
<！--整个宽度-->
<div class="container-fluid" style="border：1px solid #f00； height：100px；"></div>
```

# 3.栅格

## 3.1帮助手册：

全部CSS样式/栅格系统，htp://3.botess.com/css/#grid-options
Bootstrap提供了一套响应式、移动设备优先的流式栅格系统，随着屏幕或视口（viewport） 尺
寸的增加，系统会自动分为最多12列。

##  3.2.栅格特点

 “行（row）”必须包含在**.container** （固定宽度）或**.container-fluid** （100% 宽度）中
 “列（column）” 可以作为行 （row）” 的直接子元素。
行使用的样式“**.row**”，列使用样式“col- * - *”内容应当放置于“列（ column）”内
 列大于12时，将另起一行排列
栅格类适用于与屏幕宽度大于或等于分界点大小的设备，并且针对小屏幕设备覆盖栅格类。
 栅格参数：**“col-* * - ***”

large ：Ig
medium ：md
small:sm ：sm
X small ：XS
例如：

```html
<div class="container">
<div class="row">
<div class="col-md-3 col-xs-6">11</div>
<div class="col-md-3 col-xs-6">12</div>
<div class="col-md-3 col-xs-6">13</div>
<div class="col-md-3 col-xs-6">14</div>
</div>
<div class="row">
<div class="col -md-3 col-Xs-6">21</div>
<div class="col -md-3 col-xs-6">22</div>
<div class="col-md-3 col-xs-6">23</div>
<div class="col-md-3 col-xs-6">24</div>
</div>
</div>
```

# 4.按钮



## 4.1帮助手册：

全部CSS样式/按钮/预定义样式，http://v3.bootcss.com/ss/#buttons options

```html
（默认样式） Default （首选项） Primary （成功） Success （一般信息） Info （警告） Wamning
（危险） Danger （链接） Link
<button type="button" class="btn btn-default"> （默认样式） Default</button>
<button type="button" class="btn btn-primary"> （首选项） Primary</button>
<button type="button" class="btn btn-success"> （成功） Success</ button>
<button type="button" class="btn btn-info"> （-般信息） Info</button>
<button type="button" class="btn btn-warning"> （警告） Warning</ button>
<button type="button" class="btn btn-danger"> （危险） Danger</button>
<button type="button" class="btn btn-link"> （链接） Link</button>
```

●**.btn-lg**、 **.btn-m** 或**.btn-xs** 可以设置按钮的不同尺寸
● .active类设置按钮激活状态，其表现为被按压下去（底色更深、边框夜色更深、向内投射阴影）

# 5.响应式工具

## 5.1帮助手册：

全部CSS样式/响应式工具，http://v3.bootcss.com/css/#responsive 

|               | 超小屏幕 | 小屏幕 | 中等屏幕 | 大屏幕 |
| ------------- | -------- | ------ | -------- | ------ |
| .visible-xs-* | 可见     | 隐藏   | 隐藏     | 隐藏   |
| .visible-sm-* | 隐藏     | 可见   | 隐藏     | 隐藏   |
| .visible-md-* | 隐藏     | 隐藏   | 可见     | 隐藏   |
| .visible-lg-* | 隐藏     | 隐藏   | 隐藏     | 可见   |
| .hidden-xs    | 隐藏     | 可见   | 可见     | 可见   |
| .hidden-sm    | 可见     | 隐藏   | 可见     | 可见   |
| .hidden- md   | 可见     | 可见   | 隐藏     | 可见   |
| .hidden-1g    | 可见     | 可见   | 可见     | 隐藏   |

```xml
● 例如：
<！--
设置onediv，中等屏幕和超小屏幕显示
-->
<div class="visible-md visible-xs">one</div>
<！--
设置twodiv，小屏幕和超大屏幕隐藏
-->
<div class="hidden-sm hidden-1g">two</div>
```

## 5.2代码实现

```xml
	
<！--
1.整个topbar划分比例：1：2：1
2.中间区域只在“大屏幕”和“中等屏幕”显示
3.整个区域在“超小屏幕”英寸
-->
<div class="container topbar hidden-xs">
	<div class="row">
			<div class="col-md-3 col-sm-6">
  				  <img src="../ img/ 1ogo2. png"/>
			</div>
			<div class="col-md-6 visible-lg visible-md">
				<img src="../ img/header. jpg"/>
			</div>
			<div class="col-md-3 col-sm-6">
				<a href="" class="btn btn-danger btn-sm"> 免费注册</a>
				<a href="" class="btn btn-link btn-sm">登录</a>
				<a href="" class="btn btn-link btn-sm">购物车</a>
		</div>
	</div>
</div>
```

# 6.导航

## 6.1默认的导航栏

```xml
<nav class="navbar navbar-default" role="navigation">
    <div class="container-fluid">
    <div class="navbar-header">
        <a class="navbar-brand" href="#">菜鸟教程</a>
    </div>
    <div>
        <ul class="nav navbar-nav">
            <li class="active"><a href="#">iOS</a></li>
            <li><a href="#">SVN</a></li>
            <li class="dropdown">
                <a href="#" class="dropdown-toggle" data-toggle="dropdown">
                    Java
                    <b class="caret"></b>
                </a>
                <ul class="dropdown-menu">
                    <li><a href="#">jmeter</a></li>
                    <li><a href="#">EJB</a></li>
                    <li><a href="#">Jasper Report</a></li>
                    <li class="divider"></li>
                    <li><a href="#">分离的链接</a></li>
                    <li class="divider"></li>
                    <li><a href="#">另一个分离的链接</a></li>
                </ul>
            </li>
        </ul>
    </div>
    </div>
</nav>
```

## 6.2响应式导航栏

可以直接从文档粘贴现有代码然后修改。

```xml
<nav class="navbar navbar-default" role="navigation">
    <div class="container-fluid"> 
    <div class="navbar-header">
        <button type="button" class="navbar-toggle" data-toggle="collapse"
                data-target="#example-navbar-collapse">
            <span class="sr-only">切换导航</span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="#">菜鸟教程</a>
    </div>
    <div class="collapse navbar-collapse" id="example-navbar-collapse">
        <ul class="nav navbar-nav">
            <li class="active"><a href="#">iOS</a></li>
            <li><a href="#">SVN</a></li>
            <li class="dropdown">
                <a href="#" class="dropdown-toggle" data-toggle="dropdown">
                    Java <b class="caret"></b>
                </a>
                <ul class="dropdown-menu">
                    <li><a href="#">jmeter</a></li>
                    <li><a href="#">EJB</a></li>
                    <li><a href="#">Jasper Report</a></li>
                    <li class="divider"></li>
                    <li><a href="#">分离的链接</a></li>
                    <li class="divider"></li>
                    <li><a href="#">另一个分离的链接</a></li>
                </ul>
            </li>
        </ul>
    </div>
    </div>
</nav>
```

## 6.3导航栏表单

```html
<nav class="navbar navbar-default" role="navigation">
    <div class="container-fluid"> 
    <div class="navbar-header">
        <a class="navbar-brand" href="#">菜鸟教程</a>
    </div>
    <form class="navbar-form navbar-left" role="search">
        <div class="form-group">
            <input type="text" class="form-control" placeholder="Search">
        </div>
        <button type="submit" class="btn btn-default">提交</button>
    </form>
    </div>
</nav>
```

# 7.轮播图

```html
<div id="myCarousel" class="carousel slide">
    <!-- 轮播（Carousel）指标 -->
    <ol class="carousel-indicators">
        <li data-target="#myCarousel" data-slide-to="0" 
            class="active"></li>
        <li data-target="#myCarousel" data-slide-to="1"></li>
        <li data-target="#myCarousel" data-slide-to="2"></li>
    </ol>   
    <!-- 轮播（Carousel）项目 -->
    <div class="carousel-inner">
        <div class="item active">
            <img src="/wp-content/uploads/2014/07/slide1.png" alt="First slide">
        </div>
        <div class="item">
            <img src="/wp-content/uploads/2014/07/slide2.png" alt="Second slide">
        </div>
        <div class="item">
            <img src="/wp-content/uploads/2014/07/slide3.png" alt="Third slide">
        </div>
    </div>
    <!-- 轮播（Carousel）导航 -->
        <a class="left carousel-control" href="#myCarousel" role="button" data-slide="prev">
            <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
            <span class="sr-only">Previous</span>
        </a>
        <a class="right carousel-control" href="#myCarousel" role="button" data-slide="next">
            <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
            <span class="sr-only">Next</span>
        </a>
    <!-- 控制按钮 -->
    <div style="text-align:center;">
        <input type="button" class="btn start-slide" value="Start">
        <input type="button" class="btn pause-slide" value="Pause">
        <input type="button" class="btn prev-slide" value="Previous Slide">
        <input type="button" class="btn next-slide" value="Next Slide">
        <input type="button" class="btn slide-one" value="Slide 1">
        <input type="button" class="btn slide-two" value="Slide 2">            
        <input type="button" class="btn slide-three" value="Slide 3">
    </div>
</div> 
<script>
$(function(){
        // 初始化轮播
        $(".start-slide").click(function(){
            $("#myCarousel").carousel('cycle');
        });
        // 停止轮播
        $(".pause-slide").click(function(){
            $("#myCarousel").carousel('pause');
        });
        // 循环轮播到上一个项目
        $(".prev-slide").click(function(){
            $("#myCarousel").carousel('prev');
        });
        // 循环轮播到下一个项目
        $(".next-slide").click(function(){
            $("#myCarousel").carousel('next');
        });
        // 循环轮播到某个特定的帧 
        $(".slide-one").click(function(){
            $("#myCarousel").carousel(0);
        });
        $(".slide-two").click(function(){
            $("#myCarousel").carousel(1);
        });
        $(".slide-three").click(function(){
            $("#myCarousel").carousel(2);
        });
    });
</script>
```

