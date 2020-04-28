---
title: mysql必知必会
date:  2019-11-21
tags:
- 笔记
categories:  
- sql
---

## 第四章

## 4.6限制结果

limit 5,6; (从第下标为5 的行开始返回6条数据)	

```
LIMIT 6 OFFSET 2; 返回6个从下表1开始
```

## 第八章用通配符匹配

1. like操作符号
2.   {% :  N个}
3. ｛ _ ：一个｝

## 第九章 正则表达式

例子：where name REGEXP ' 字符串 '

### 9.2.1基本字符串匹配

【 . 】可以匹配任何字符串

> **匹配不区分大小写**MySQL中的正则表达式匹配（ 自版本
> 3.234后）不区分大小写（即，大写和小写都匹配）。为区分大
> 小写，可使用BINARY关键字，如WHERE prod_ name REGEXP
> **BINARY**  ' JetPack . 000 '
>
> LIKE和REGEXP之间有一个重要的差别。
> 	**LIKE匹配整个列。如果被匹配的文本在列值**
> **中出现，LIKE将不会找到它，相应的行也不被返回（除非使**
> **用通配符）**。而REGEXP在列值内进行匹配，如果被匹配的文
> 本在列值中出现，REGEXP将会找到它，相应的行将被返回，
> 这是一个非常重要的差别。
> REGEXT能不能用来匹配整个列值（从而起与LIKE相同的作用） ？答案是肯定的，使用^和$定位符（ anchor）即可，
> 本章后面介绍。
>
> 

### 9.2.2	or匹配

为搜索两个串可以使用 | 来匹配字符串。可以使用两个以上 的  【 | 】符号。

```mysql
SELECT prod_name
FROM products
WHERE prod_name REGEXP '1000|2000'
ORDER BY prod_name
```

+------------------+
| JetPack 1000|
| JetPack 2000|
+------------------+

### 9.2.3[]匹配几个字符之一

[123456789] ==[1-9]  

集合中的一个，用[ 123 ] 等价于1|2|3

```mysql
SELECT prod_name
FROM products
where prod_name REGEXP '1|2|3 Ton'
ORDER BY prod_name
```

**输出**
1 ton anvil
2 ton anvil
JetPack 1000
JetPack 2000
TNT （1 stick）
**字符集合也可以被否定**:匹配该集合意外的集合 

例如：【^123】



### 9.2.5 \\\匹配特殊字符

\\\ ：两个斜杠表示转义  

```mysql
\\k  换页
\\n  换行
\\r  回车
\\t  制表
\\v  纵向制表
```

### 9.2.6[:digit:]匹配字符类

```
字符类
[:alnum:] = [a-zA-Z1-9]
[:alpha:] = [a-zA-z]
[:blank:] = [\\t]
[:cntrl:] = asc110-31和127的控制符
[:digit:] = [0-9]
[:graph:] = 同[:print:]
[:lower:] = 任意小写字母
[:punct:] = 既不在[:alnum:]又不在[:cntrl:]中的任意字符
[:space:] 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]）
[:upper:] 任意大写字母（同[A-Z]）
[:xdigit:] 任意十六进制数字（同[a-fA-F0-9]）

```

### 9.2.7 * + ？匹配多个实例

```mysql
*      :  0或多
+      :  1或多（等于{1,}）
?      :  0个或1个匹配（等于{0,1}）
{n}    :  指定数目的匹配
{n,}   :  不少于指定数目的匹配
{n,m}  :  匹配数目的范围（m不超过255）
SELECT prod_name
FROM products
WHERE PROD_NAME REGEXP '[0-9]{4}' 等价 '[[:digit:]]{4}'
ORDER BY prod_name
```

### 9.2.8 ^￥定位符

```mysql
^ 文本的开始
$ 文本的结尾
[[:<:]] 词的开始
[[:>:]] 词的结尾
```



## 第十章创建计算字段

### 10.1计算字段

中间字段

### 10.2拼接字段

- CONCAT（） ：中间用逗号分隔开
- RTrim()删除右空白
- LTrim()删除左空白，
- 拼接字段没名字可用**AS**

```mysql
SELECT CONCAT(vend_name,' ( ',RTRIM(VEND_COUNTRY) ,' ) ') AS 国家
FROM VENDORS
ORDER BY VEND_NAME DESC
```

### 10.3执行计算

计算字段： 

```
SELECT 	 
prod_ID,
QUANTITY,
ITEM_PRICE,
QUANTITY*ITEM_PRICE AS EXPANDED_PRICE
FROM ORDERITEMS
WHERE ORDER_NUM = 20005
```

QUANTITY * ITEM_PRICE **AS** EXPANDED_PRICE 是计算字段

## 第十一章使用数据处理函数

### 11.2使用函数

### 11.2.1文本处理函数

Upper（）转换大写，是将查询出的文本

```
Left() 返回串左边的字符
Length() 返回串的长度
Locate() 找出串的一个子串
Lower() 将串转换为小写
LTrim() 去掉串左边的空格
Right() 返回串右边的字符
RTrim() 去掉串右边的空格
Soundex() 返回串的SOUNDEX值 找出发音相似的
SubString() 返回子串的字符
Upper() 将串转换为大写
```

### 11.2.2日期和时间处理函数

```mysql
AddDate() 增加一个日期（天、周等）
AddTime() 增加一个时间（时、分等）
CurDate() 返回当前日期
CurTime() 返回当前时间
Date() 返回日期时间的日期部分
DateDiff() 计算两个日期之差
Date_Add() 高度灵活的日期运算函数
Date_Format() 返回一个格式化的日期或时间串
Day() 返回一个日期的天数部分
DayOfWeek() 对于一个日期，返回对应的星期几
Now() 返回当前日期和时间
Time() 返回一个日期时间的时间部分
Year() 返回一个日期的年份部分
Month() 返回一个日期的月份部分
Hour() 返回一个时间的小时部分
Minute() 返回一个时间的分钟部分
Second() 返回一个时间的秒部分
```

```mysql
SELECT 	 
cust_id,order_num
FROM orders
WHERE Data（order_date）='2005-09-01'
匹配字段中的日期
Time（）可以提取出日期
```

### 11.2.3数值处理函数

```
Abs() 返回一个数的绝对值
Cos() 返回一个角度的余弦
Exp() 返回一个数的指数值
Mod() 返回除操作的余数
Pi() 返回圆周率
Rand() 返回一个随机数
Sin() 返回一个角度的正弦
Sqrt() 返回一个数的平方根
Tan() 返回一个角度的正切
```

## 第十二章汇总数据

### 12.1.1AVG（）函数

​		AVG（）求**平均值**函数也可返回特定行和列的特定值

### 12.1.2 count(   )函数

- count( *)/count( )计算行数，不分空值还是null都计算在内。

-  使用COUNT(column)对特定列中具有值的行进行计数，忽略 

  NULL值。

### 12.1.3   MAX（）函数

- 返回指定列中最大值。max（column）忽略列值为null的行。
- 在用于文本数据时，如果数据按相应的列排序，则MAX(column)返回最后一行。

### 12.2.4   min（）函数

​		

### 12.2.5    SUM（）函数

- SUM()函数忽略列值为NULL的行。


### 12.2 聚集不同值

- **DISTINCT**（去重）

-  **ALL**的使用

note : 不指定DISTINCT就是ALL，

### 12.3  组合聚集函数

- 多个聚合函数并行使用。


## 第十三章分组数据

## 13.1数据分组

group by 

```
SELECT vend_id , count(*) AS num_prods
from products
GROUP BY vend_id
1001 3
1002 2
1003 7
1005 2
```



## 13.2创建分组

- GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套， 为数据分组提供更细致的控制。 
- 如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上 进行汇总。换句话说，在建立分组时，指定的所有列都一起计算 （所以不能从个别的列取回数据）。 
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式 （但不能是聚集函数）。如果在SELECT中使用表达式，则必须在 GROUP BY子句中指定相同的表达式。不能使用别名。 
- 除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子 句中给出。 
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列 中有多行NULL值，它们将分为一组。 
- GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。

### 13.3 过滤分组

- where是过滤行

- Having是过滤分组

note：WHERE在数据 分组前进行过滤，HAVING在数据分组后进行过滤。这是一个重要的区别

### 13.4  分组和排序

分组不保证输出顺序

### 13.5 select子句顺序

|   子句   |         说明         | 是否必须 |
| :------: | :------------------: | :------: |
|  SELECT  | 要返回的列或者表达式 |          |
|   FROM   |   从中检索数据的表   |          |
|  WHERE   |       行级过滤       |          |
| GROUP BY |         分组         |          |
|  HAVING  |       分组过滤       |          |
| ORDER BY |       输出排序       |          |
|  LIMIT   |      要检索的行      |          |



## 第十四章使用子查询

### 14.2  利用子查询 进行过滤

子查询的主要作用就是过滤where in 组合查询

### 14.3  作为计算字段使用子查询

表.类名=全限定名

## 第十五章联结表

### 15.1联结

用一条select语句查询多张表

### 15.1.1关系表

主外键约束

### 15.1.2为什么要使用联结

用一条select语句查询多张表

## 15.2创建联结

通过主外键进行联结，where能够起到过滤的作用

### 交叉连接 = 笛卡尔积

cross join，交叉联接返回左表中的所有行，左表中的每一行与右表中的所有行组合。交叉联接也称作笛卡尔积

### 内部连接 = 笛卡尔积

**等值连接（**交叉连接**）：主表  INNER JOIN 从表 on 条件（主外键）在连接条件中使用等于号(=)运算符比较被连接列的列值，其查询结果中列出被连接表中的所有列，包括其中的重复列。

**不等值连接：**在连接条件使用除等于运算符以外的其它比较运算符比较被连接的列的列值。这些运算符包括>、>=、<=、<、!>、!<和<>。

### 外连接

1. 左外联结：表1 left outer join 表2  on 表1.主键=表2.副键  右边补空
2. 右外联结：表1 right outer join 表2  on 表1.主键=表2.副键 左边补空

### 全外连接

MySQL目前不支持此种方式，可以用其他方式替代解决。

左边的null会显示，右边的null也会显示

### 15.2.3  联结多个表-自然连接

```
select prod_name,vend_name,prod_price,quantity 
from orderitems,products,vendors
where products.vend_id = vendors.vend_id
and orderitems.prod_id = products.prod_id 
and order_num=20005
```



## 第十六章 创建高级联结

### 16.1使用表别名 mysql

关键字：AS

### 16.2使用不同类型的联结

#### 16.2.1自联结

两张表 用 on

```
select prod_id ,prod_name
from products
where vend_id = (select vend_id from products where prod_id = 'DNTTR')
```

```
select p1.prod_id ,p1,prod_id 
from products as p1,products as p2
where p1.vend_id = p2.vend_id and p2.prod_id = 'DTNTR'
```

#### 16.2.2 自然联结

 三张表不用on  

```
from 表A,表B,表C 
where 条件1 and 条件2 and 条件3 
-- 自然连接
```

#### 16.2.3 外部联结

```
	left outer join
	right outer join
```

#### 16.3 使用带聚集函数的联结

检索所有客户以及客户下单的总数

```mysql
select customers.cust_name, customers.cust_id,
count(orders.order_num) as num_ord 
from customers inner join orders
on customers.cust_id = orders.cust_id 
group by customers.cust_id
```



## 第十七章组合查询

## 17.1UNION规则（并）

1.  select语句用UNION关键字分隔；
2.  查询的列相同
3.  列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）
4. 全集：union all 取消去重 
5.  交集：intersect
6.  差集：minus

## 17.2多组合结果排序

　　只能使用一条ORDER BY子句，且必须出现在最后一行

## 18.全文检索

## 19.创建表

create table 表名  if not exists （

列名1       数据类型       not null   auto_increment,
列名 2       数据类型       null
列名 3       数据类型       null 

primary key(列名1)

）engine=innodb

### 19.1 使用null值

### 19.2 主键再介绍

### 19.3 使用auto_increment

### 19.4 使用默认值

### 19.5 更新表

```
alter table verdors add vend_phone char(20)//添加字段
alter table vendors drop vend_phone; //删除字段
alter table orderitems add constraint fk_orderitems_orders foreign key (order_num)references orders (order_num)
```

### 19.6 删除表

### 19.7 重命名表

rename table customers2 to customers

## 20.插入数据

**不安全**：需插入完整数据，不写数据的用null，按顺序添加数据，mysql自增的也要制定为null

**安全**：需要指定**列名**然后面添加**值**，表结构改变也

**可省略列**：但是必须是允许为null值，或者设置默认值

**插入多行数据**：insert into (列名) values (),();

**插入select的结果到表中**：（只关心列的数据类型，不关心名字）

inset into (列名) select （列名）from  表

## 21.更新数据

更新要指定某一行，否则将更新所有

update 表名 set cust_email = '------------'

**删除**：TRUNCATE实际是删除原来的表并重新创建一个表，而不是逐行删除表中的数据

**更新和删除的指导原则** ：。

- 一定加where条件
- 保证都有主键
- 先用select测试

## 22 视图（子查询的升级版）

视图只包含使用时动态检索数据的查询，作为试图它不包含任何列或数据，

### 22.1 为什么使用视图

- 重用复杂sql语句
- 使用表的组成部分而不是整个表
- 保护数据给表的部分数据授予权限，而不是整张表的权限
- 可以更改原表的数据类型
- 只可用于 查询、过滤、排序、添加、更新数据（更新视图将更新原表的数据）

性能：使用嵌套视图可能影响性能

### 22.2 视图的规则和限制

- 视图名字唯一，可以嵌套，可以和表一起使用
- 需要相应的权限
- 外层的order by 会把里面的order by 覆盖掉
- 视图不能有索引和相关的触发器

### 22.3 使用视图

- create view 

- show create view 名字

- drop view 名字

  ```
  create view productcustomers AS  复杂查询语句
  ```

  

- 创建视图相当于某个中间表，进而免的频繁创建表
- 视图有以下情况不能更新
  1. 分组
  2. 联结
  3. 子查询
  4. 并 （union）
  5. 聚合函数
  6. distinct
  7. 导出列

## 23 使用存储过程

- 创建过程

```
create procedure 名字（in 参数1，out 参数2）
begin
	sql语句
end
```

- 执行存储过程

```
call productpricing(@pricelow
@pricehigh
@priceaverage
)
```

- 删除存储过程

 ```
 drop procedure 名字
 ```

note：不要加（）

- 使用参数

```
创建存储过程	create prodecure 名字（in 参数1，out 参数2）
使用存储过程	call 名字（@参数1，@参数2，@参数3）
删除存储过程 drop prodecure
create procedure ordertotal(
	IN onnumber INT, //传给存储过程
	IN taxable BOOEAN,
	OUT OTOTAL DECIMAL //从存储过程传出
	//inout:对存储过程传入和传出
)
BEGIN
	DECLARE total DECIMAL(8,2);
	DECLARE taxrate INT default 6 ;
	
	select SUM(item_price*quantity)
	from orderitems
	where orderitems
	into total;
	-- if和end if 是一套语法
	if taxable then 
		select total+(total/100*taxrate) into total ; 
	end if; 
    	select total into ototal;
end;

declare： 声明了两个局部变量
decimal (8,1)：小数点前面8位，后面是2位且在0-8之间
into:将前面的后再后面的变量内。in out declare 等变量
-----------------------
call productpricing(20005，0，@total);
select @total;
```

参数的数据类型：存储过程的数据类型和表中的数据类型一样

## 24游标

- 游标：是结果集可以在选择的浏览和更改其中的数据，且只能用于存储过程

- 创建游标

  ```
  create procedure processorders()
  begin 
  	declare done BOOLEAN default 0;
  	declare o int;
  	declare t decimal(8,2);
  	declare ordernumbers cursor
  	for
  	--给这个查询语句设置游标
  	select order_num from orders;
  	declare continue handkler for sqlstate '02000' set done =1;
  	create table if not exists ordertotals(order_num int,total decimal(8,2));
  	--使用游标
  	open ordernumbers;
  	--循环检索
  	REPEAT	
  	--游标检索
  	    FETCH ordernumbers INTO o;
  		call ordertotal(o,1,t);
  		
  		insert into ordertotal(o,1,t)
  		values(o,t);
  	until done end repeat
  	--关闭游标
  	close ordernumbers
  end;
  
  ```

## 25 触发器

命名空间：表内

删除触发器：

```
drop trigger newproduct //先删除后改
```

note:before 用于数据的净化

delete删除触发器

​	1.在old里面使用一个名为old的虚拟表，访问被删除的行

​	2.old表是只读的

```mysql
create trigger deleteorder before delete on orders
for each row 
begin
 insert into archive_orders(order_num,order_date,cust_id)values(old.order_num,old.order_date,old.cust_id);
 end
 --么名字，跟哪个表有关系，什么语句触发（增删改），什么时候触发
 --触发器失效：后面的相应的sql语句不执行
```

update触发器

- old访问以前的值，new访问新的值    

- new是跟新值跟新别人。

  

## 26管理事务

mysql不没人支持事务

--开启事务

start transaction;

--保留点

savepoint delete1

--回滚

rollback to delete1

--提交

commit

--更改默认提交行为

 set autocommit =0(假)

## 29全球化和本地化

显示字符集：show character set

校对：show collation

```
create table mytable(
	columnn1 int,
	colummn2 varchar(10)
)default charactor set hbrew
collate hebrew_general_ci;
--不指定则使用默认的
--排序时指定不同的校对顺序可以人为的指定，也可使用在havin 聚集函数
```

## 30 安全管理

1.创建一个新用户账号

```
create user Kirkzhang identified by '123'
```

2.重命名表

```
rename user Kirkzhang to shiming
```

3.删除表

```
drop user shiming 
```

- 访问权限设置

  ```
  grant select on crashcourse.* to Kirkzhang
  Kirkzhang用户对crashcourse数据库种具有只读权限（select）
  ```

​       

```
revoke select on crashcourse.* from Kirkzhang
```

作用范围：

​		整个服务器：grant all和revoke all 

​		整个数据库：on database.*

​		特定表：on database.table 

​		特定列；

​		特定存储过程

​		权限表

简化授权：

```
grant select ,insert on crashcourse.* to Kirkzhang
```

更改口令

​		set password for  Kirkzhang = password('new123');

## 31 数据库的维护

flush table :保证所有数据写入磁盘（备份数据）

## 32 改善性能

- 查看设置查看线程也可以用explain解释然后杀死他们。

--------------------------------------------------------------------------------------

查看设置：show variables  ；show status；

所有线程：show processlist 

杀死线程：kill  

让mysql 解释语句：explain

--------------------------------------------------------------------------------------

delayed关键字

复杂or影响性能

like很慢

- 存储过程比一条语句要快


- 在导入数据时，应该关闭自动提交。你可能还想删除索引（包括 
  FULLTEXT索引），然后在导入完成后再重建它们。

索引改善性能，但是会影响插入，删除，更新，速度，

经过一些列操作，表的结构会改变

最重要的规则就是，每条规则在某些条件下都会被打破。

## 33 数据类型

- 串数据类型char-varchar：存名字，地址 ,用单引号

  ```
  CHAR 		1～255个字符的定长串。它的长度必须在创建时指定，否则MySQL假定为CHAR(1)
  ENUM 		接受最多64 K个串组成的一个预定义集合的某个串
  LONGTEXT 	与TEXT相同，但最大长度为4 GB 
  MEDIUMTEXT  与TEXT相同，但最大长度为16 K 
  SET 		接受最多64个串组成的一个预定义集合的零个或多个串
  TEXT 		最大长度为64 K的变长文本
  TINYTEXT 	与TEXT相同，但最大长度为255字节
  VARCHAR 	长度可变，最多不超过255字节。如果在创建时指定为VARCHAR(n)，则可存储0到n个字符的变长串			 （其中n≤255
  ```

- 数值类型

  ```
  BIT 		位字段，1～64位。（在MySQL 5之前，BIT在功能上等价于TINYINT
  BIGINT 		整数值，支持9223372036854775808～9223372036854775807（如果是UNSIGNED，为0～				18446744073709551615）的数
  BOOLEAN		（或BOOL） 布尔标志，或者为0或者为1，主要用于开/关（on/off）标志
  DECIMAL		（或DEC） 精度可变的浮点值
  DOUBLE 		双精度浮点值
  FLOAT 		单精度浮点值
  INT			（或INTEGER） 整数值，支持2147483648～2147483647（如果是UNSIGNED， 为0～				4294967295）的数
  MEDIUMINT 	整数值，支持8388608～8388607（如果是UNSIGNED，为0～16777215）的数
  REAL 		4字节的浮点值
  SMALLINT 	整数值，支持32768～32767（如果是UNSIGNED，为0～65535）的数
  TINYINT 	整数值，支持128～127（如果为UNSIGNED，为0～255）的数
  ```

- 日期类型

  ```
  DATE 		表示1000-01-01～9999-12-31的日期，格式为YYYY-MM-DD
  DATETIME 	DATE和TIME的组合
  TIMESTAMP 	功能和DATETIME相同（但范围较小）
  TIME 		格式为HH:MM:SS
  YEAR 		用2位数字表示，范围是70（1970年）～69（2069年），用4位数字表示，范围是1901年～2155年
  ```

- 二进制类型

```
BLOB 		Blob最大长度为64 KB 
MEDIUMBLOB 	Blob最大长度为16 MB 
LONGBLOB 	Blob最大长度为4 GB 
TINYBLOB 	Blob最大长度为255字节
```



# 第34章 mysql进阶

## 34.1 case when 

```
case 
///
end
```



1. CASE sex

   ​          when ' 1 ' then ' 男 '  

   ​		 when ' 2 ' then  ' 女 '

   ​	   else  ' 其他'  end 

2. CASE 

   when sex = ' 1 ' then ' 男 '

   when sex = ' 2 ' then  ' 女 '

   else ' 其他 '  end

3. CASE 

   when col_1 in ('a','b') then '第一类'

   when col_2 in ('a') then '第二类'

   else '其他' end

4. 和sum一起使用

     

    ```
    select 
    	sum( case u.sex when 1 then 1 else 0 end)男性
    	sum( case u.sex when 2 then 1 else 0 end)女性
    	sum( case u.sex <>1 and u.sex <>2 then 1 else 0 end) 性别为空
    from user as u
    	男性       女性       性别为空
    ---------- ---------- ----------
        3          2          0
    ```

    

```
select
      count(case when u.sex=1 then 1 end)男性,
      count(case when u.sex=2 then 1 end)女,
      count(case when u.sex <>1 and u.sex<>2 then 1 end)性别为空
from users as  u;
 
   男性        女       性别为空
---------- ---------- ----------
    3          2          0

```

