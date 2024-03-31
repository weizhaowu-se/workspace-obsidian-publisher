---
share: "true"
title: javascript高级程序设计笔记
date: 2017-07-18 21:12:05
tags:
  - JavaScript
---
1. 使用var定义的变量为局部变量
* 省略var定义的变量为全局变量
	<!--more-->
* 五种简单数据类型
	* Undefined
		* 声明变量但是未初始化时变量的值
	* Null
		* 表示一个空对象的指针,在变量还没有真正保存对象时,**应该明确让其保存null值**.
	* Boolean
		* ![](http://7xkzud.com1.z0.glb.clouddn.com/17-7-20/49622837.jpg)
		* 任何非空字符串为真
		* 任何非零数字值为真,0和NaN为假
	* Number
		* isNaN() 函数确实一个值是否"不是数值",注意其判断的过程会检测其toString的值,例如isNaN('1')返回false(意思是'1'是一个数字)
		* ![](http://7xkzud.com1.z0.glb.clouddn.com/17-7-20/6387097.jpg)
		* parseInt函数
		* ![](http://7xkzud.com1.z0.glb.clouddn.com/17-7-20/20388342.jpg)
	* String
* 复杂数据类型
	* Object
* 变量,作用域和内存问题
	* 在if以及for语句中定义的变量在循环执行结束后依旧存在.
* Array数组的使用//todo
	* 创建数组的几种不同方式
		* var colors = new Array()
		* var colors = new Array(3)
		* var colors = new Array('A', 'B')
		* var colors = []
	* 注意,数组的length属性的动态性,可以通过直接设置其值来达到动态调整数组长度的目的
	* Array.isArray(colors)方法判断是否是数组
	* concat方法拼接返回新的数组
	* slice方法
		* 一个参数时返回开始位置直至结束的数组
		* 两个参数时返回指定位置之间的数组(包含开始但不包含结束位置的项
	* splice方法
		* 参数一:删除的第一项的位置
		* 参数二:删除的项数
		* 参数三,四,...n:插入的值
	* indexOf方法,从头开始查找,不存在返回-1
	* lastIndexOf,从结尾开始查找,不存在返回-1
	* 迭代方法
		* every()
		* filter()
		* forEach()
		* map()
		* some()
		* 注意,迭代方法的参数均为一个函数,此函数的参数为(item,index,array)
		* ![](http://7xkzud.com1.z0.glb.clouddn.com/17-7-23/56979653.jpg)
	* 缩小方法(进行数组的迭代求和等)
		* reduce(function(prev, cur, index, array) {})
		* reduceRight(function(prev, cur, index, array) {})
* Date类型的使用 //todo
