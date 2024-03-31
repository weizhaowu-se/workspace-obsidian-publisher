---
share: "true"
title: python-tips
date: 2018-01-28 22:11:30
tags:
  - python
  - tips
---
# python-tips
## 列表生成式
<!--more-->
* Python内置的非常简单却强大的可以用来**创建list**的生成式
* 写列表生成式时，把要生成的元素x * x放到前面，后面跟for循环，就可以把list创建出来
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-28/37747048.jpg)
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-28/79156020.jpg)

## 生成器
* 当我们需要创建100万个元素的列表的时候,如果这些元素可以按照一定的规则去生成,那么我们就不需要在内存中同时维护这100万个元素,而是通过在迭代中边去生成元素来实现.
* 把一个列表生成式的[]改成()，就创建了一个generator
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-28/48319854.jpg)
* 需要注意的是,我们一般不会显式调用next方法,而是使用for in迭代
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-28/22622518.jpg)
* 如果推算的算法比较复杂，用类似列表生成式的for循环无法实现的时候，还可以用函数来实现。
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/18-1-28/80097627.jpg)
	* generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。


