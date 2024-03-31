---
share: "true"
title: Java中的synchronized和volatile
date: 2016-10-26 14:08:22
tags:
  - java
---
## volatile关键字
* 程序在运行时,为了提高性能可能会将主存中的变量拷贝到CPU缓存当中,
	* ![](http://7xkzud.com1.z0.glb.clouddn.com/16-10-26/52900619.jpg)
<!--more-->
* 如果是非volatile变量,那么就可能会出现这样的问题:	
	* 线程1读取变量并且进行修改,在还没有将其写会主存的时候,线程2读取同一个变量此时就出现了问题.因为非volatile变量无法保证什么时候从主存中读取数据,也无法保证什么时候写回主存.
	* 图示 ![](http://7xkzud.com1.z0.glb.clouddn.com/16-10-26/49586736.jpg)
* volatile变量则可以确保了变量的修改会及时写回主存、变量直接从主存中读取

## synchronized关键字

>引用 [http://tutorials.jenkov.com/java-concurrency/volatile.html](http://tutorials.jenkov.com/java-concurrency/volatile.html)

