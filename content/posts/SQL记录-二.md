---
share: "true"
title: SQL记录(二)
date: 2017-07-31 21:27:57
tags:
  - SQL
  - 数据库
---

# 聚集函数
### MIN()
### MAX()
### AVG()
### SUM()
<!--more-->
### COUNT() 函数
* count(*) 将对表中行的数目进行计数
* count(column) 会略过值为null的列
### tips
*可以使用distinct关键字仅指定不同值的行.
	>select count(distinct column)  
	>.....
# **分组数据**
关键字:  
### **Group by**
* 根据指定的列来进行分组
	>select cID, count(*) as number
	>from Products
	>Group by cID;
* 根据cID进行分组,返回cID以及相对应的数目
* 除了聚集计算语句之外,select的每个字段必须为分组的依据(出现在group by之后)
* group by 出现在where子句之后,order by之前.    
### **Having**
* 筛选分组
* HAVING和WHERE的区别在于一个是在分组之前过滤(where,不符合条件的记录不参与分组),一个是在分组之后进行过滤(HAVING).
# 联结 join
* 等值联结,又称内联结 
	* inner join on
	* where ... ** = ** 
* 





