---
title: leetcodeSQL笔记
date:  2019-11-21
tags:
- 笔记
categories:  
- sql
---
#### [176. 第二高的薪水](https://leetcode-cn.com/problems/second-highest-salary/)

1. 用ifnull（参数一，null）as # 来返回null值

2. 用limit返回第n个数据 limit # offset (下标值)

   ```mysql
   select distinct Salary from Employee e where N = (select count(distinct Salary) from Employee where Salary >= e.Salary )
   ```

#### [177. 第N高的薪水](https://leetcode-cn.com/problems/nth-highest-salary/)

```mysql
-- 自定义函数
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (
      SELECT MAX(Salary) FROM Employee E1
      WHERE N =
      (SELECT COUNT(DISTINCT(E2.Salary)) FROM Employee E2
      WHERE E2.Salary >= E1.Salary)
  );
END
```

-------------------

```mysql
-- 自定义函数
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN

  RETURN (
     # Write your MySQL query statement below.
       select distinct Salary from Employee e where N = (select count(distinct Salary) from Employee where Salary >= e.Salary )
  );
END
```

