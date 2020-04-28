---
title:  Integer类
date: 2019-11-08
categories: 
- SE
tags: 
- 笔记
---

1.如果Integer类型的两个数相等，如果范围在-128~127（默认），那么用“==”返回true，其余的范会false。

2.两个基本类型int进行相等比较，直接用==即可。

3.一个基本类型int和一个包装类型Integer比较，用==也可，比较时候，Integer类型做了拆箱操作。

4.Integer类型比较大小，要么调用Integer.intValue()转为基本类型用“==”比较，要么直接用equals比较。

5.integer会产生空指针异常

![](https://gitee.com/kirk_zhang/KirkzhangBlog/raw/master/images/se/Integer会产生空指针异常.png)

所以要注意，毕竟他是一个类。

## 2. integer的比较

1. 可以使用.equals()

2. “==”
3. compare(int x,inty )//是基本数据类型
4. compareTo(Integer x) //是包装类 ，其底层还是调用的compare，一定要记得判断null。