## 第八章用通配符匹配

##### 8.1   like操作符号

##### 8.1.1    百分号% 是匹配N个字符

##### 8.1.2   下划线_ 通配符只匹配一个字符

## 第九章 正则表达式

### 9.1正则表达式介绍

为了应付更复杂查询，通配符显得力不从心，所以选择正则表达式

### 9.2使用正则表达式

where name REGEXP ' 字符串 '

### 9.2.1基本字符串匹配

【 . 】可以匹配任何字符串

> **匹配不区分大小写**MySQL中的正则表达式匹配（ 自版本
> 3.234后）不区分大小写（即，大写和小写都匹配）。为区分大
> 小写，可使用BINARY关键字，如WHERE prod_ name REGEXP
> **BINARY**  ' JetPack . 000 '
>
> LIKE与REGEXP在LIKE和REGEXP之间有一个重要的差别。
> 	**LIKE匹配整个列。如果被匹配的文本在列值**
> **中出现，LIKE将不会找到它，相应的行也不被返回（除非使**
> **用通配符）**。而REGEXP在列值内进行匹配，如果被匹配的文
> 本在列值中出现，REGEXP将会找到它，相应的行将被返回，
> 这是一个非常重要的差别。
> REGEXT能不能用来匹配整个列值（从而起与LIKE相同的作用） ？答案是肯定的，使用^和$定位符（ anchor）即可，
> 本章后面介绍。
>
> 

### 9.2.2	进行or匹配

为搜索两个串可以使用 | 来匹配字符串。可以使用两个以上 的  【 | 】符号。

SELECT prod name
FROM products
WHERE prod_ name REGEXP ， '1000 | 2000 '
ORDER BY prod name；
prod_ name
+------------------+
| JetPack 1000|
| JetPack 2000|
+------------------+

### 9.2.3匹配几个字符之一

匹配任何单一的字符，但是只匹配特定的几个字符。可以使用 [ ××× ] 等价于1|2|3

SELECT prod_name
FROM products
WHERE prod_ name REGEXP '1|2|3 Ton'
ORDER BY prod name；
**输出**
1 ton anvil
2 ton anvil
JetPack 1000
JetPack 2000
TNT （1 stick）
分析 这并不是期望的输出。两个要求的行被检索出来，但还检索出
了另外3行。之所以这样是由于MySQL假定你的意思是'1'或
2或'3ton'。除非把字符|括在一一个集合中， 否则它将应用于整个串。
**字符集合也可以被否定**，即，它们将匹配除指定字符外的任何东西。
为否定一个字符集，在集合的开始处放置一个^即可。 因此，尽管【123】以配字符1、2或3. [^1231] 却匹配除这些字符外的任何东西。

### 9.2.4匹配范围

[123456789] 某一个位置匹配1-9中的某个数，可以使用[1-9]代替

![1561040773100](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561040773100.png)

### 9.2.5匹配特殊字符

正则表达式语言由具有特定含义的特殊字符构成。我们已经看到.
【】、|和-等，还有其他一些字符。 如果你需要匹配这些字符，应该
怎么办呢？为了匹配特殊字符，必须用\\\为前导。 \\\\\-表示查找-，\\\\.表示查. 这要是匹配反斜杠自身则需要。

![1561038762732](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561038762732.png)

### 9.2.6匹配字符类

![1561039161519](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561039161519.png)

### 9.2.7匹配多个实例

![1561039702459](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561039702459.png)

SELECT prod_ name
FROM products
WHERE prod_ name REGEXP ' \\\（【0-9】 sticks？\\\）'
ORDER BY prod_ name；

TNT （1 stick）
TNT （5 sticks）
正则表达式\\（【0-9】 sticks？\\）需要解说一下。 \（匹配），
分析：
【0-9】匹配任意数字（这个例子中为1和5），sticks？匹配
stick和sticks （**s后的？使s可选，因为？匹配它前面的任何字符的0次**
**或1次出现**），\）匹配）。没有？，匹配stick和sticks会非常困难

### 9.2.8定位符

目前的匹配都是匹配人任意位置的字符串，但是如果想匹配特定位置的文本。

![1561040541316](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561040541316.png)

![1561041386954](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561041386954.png)

LIKE	表明在字段内匹配整个字符串。

REGEXP  只要子串中有与模式串匹配的字符串就返回

![1561042564091](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561042564091.png)

## 第十章创建计算字段

### 10.1计算字段

![1561089720523](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561089720523.png)

### 10.2拼接字段

select CONCAT(vender_name,' ( ' ,vender_contry,' ) ' )

from vendors

order by vend_name 

RTrim()函数能删除字段右边空白的地方，LTrim()函数删除左面的空白，组装列没有名字，但是我们可以起别名方便引用这些组合列 。使用**AS关键字**来使用别名。

SELECT CONCAT(VENDER_NAME,' ( ',RTRIM(VENDER_CONTRY) ,' ) ') **AS** VENDER_TITLE

FROM VENDORS

ORDER BY VENDER_NAME DESC

### 10.3执行计算

![1561099256144](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561099256144.png)

## 第十一章使用数据处理函数

### 11.1函数

### 11.2使用函数

### 11.2.1文本处理函数

Upper( )

![1561100510494](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561100510494.png)

![1561101407867](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561101407867.png)

### 11.2.2日期和时间处理函数

myusql日期格式：yyyy-mm-dd.。如果列中出现更具体的时期，然后检索却出现不精确的情况这有可能出现检索失败。这是可以考虑使用Date（）函数

![1561103624609](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561103624609.png)![1561103731621](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561103731621.png)

### 11.2.3数值处理函数

数字处理函数仅仅处理数值数据。这些数据用于代数，三角或者集合运算。因此没有串或者日期-时间处理函数那么频繁

![1561104058260](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561104058260.png)

## 第十二章汇总数据

### 12.1.1AVG（）函数

​		AVG（）求**平均值**函数也可返回特定行和列的特定值

### 12.1.2 count(   )函数

​		count( )计算行数，不分空值还是null都计算在内。对特定列进行计算将忽律null值。

### 12.1.3   MAX（）函数

​		返回指定列中最大值。max（）忽略列值为null的行。

### 12.2.4   min（）函数

​		MIN（）的功能正好与MAX（）功能相反。

### 12.2.5    SUM（）函数

​		SUM（）返回指定列值得和。

### 12.2 聚集不同值

​		**DISTINCT**（去重）和**ALL**的使用,不指定DISTINCT就是ALL，

### 12.3  组合聚集函数

​		多个聚合函数并行使用。

select **count(*) AS 别名**，**MIN(PROD_PRICE)** , **MAX(PROD_MAX)** , **AVG(PROD_PRICE)**  FROM PRODUCTS;

## 第十三章分组数据

## 13.1数据分组

## 13.2创建分组

​		分组是在select语句的GROUP BY子句中建立的。

![1561734116039](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561734116039.png)![1561734782914](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561734782914.png)

having和where的区别就是where过滤行，**having过滤分组**。having支持所有where操作符

### 13.4  分组和排序

​		![1561772999924](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561772999924.png)

### 13.5 select子句顺序

![1561773620404](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561773620404.png)

## 第十四章使用子查询

### 14.1  子查询

### 14.2利用子查询 进行过滤

![1561775280650](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561775280650.png)

### 14.3作为计算字段使用子查询

## 第十五章联结表

### 15.1联结

### 15.1.1关系表

### 15.1.2为什么要使用联结

![1561778035743](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561778035743.png)

### 15.2创建联结

### 15.2.2  内部联结

​		**交叉查询**-------from 表1，表 2（笛卡尔积）

​               **内联结**					1.显示内联结查询。

主表  INNER JOIN 从表 on

​								   2.隐式内联结查询

 select * from 表1，表2  where主表.主键=从表.外键

### 		  外连接

​	1.左外联结：表1 left outer join 表2  on 表1.主键=表2.从键  右边补空

​	2.右外联结：表1 right outer join 表2  on 表1.主键=表2.从键 左边补空

### 15.2.3  联结多个表

## 第十六章创建高级联结

### 16.1使用表别名 

### 16.2使用不同类型的联结

### 16.2.1自联结

​		

## 第十七章组合查询

## 17.1UNION规则

　　1.UNION必须由两条或两条以上的SELECT语句组成，语句之间用UNION关键字分隔；

　　2.UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各列不需要以相同的次序列出）；

###### 　　3.列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）


![1561942061368](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1561942061368.png)

## 17.2多组合结果排序

　　在用UNION组合查询时，**只能使用一条ORDER BY子句，且必须出现在最后一条SELECT语句之后**。使用union可以极大的简化复杂的where子句，然后简化数据。