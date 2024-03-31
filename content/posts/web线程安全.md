---
share: "true"
title: web线程安全
date: 2018-01-24 10:34:17
tags:
  - java
  - spring
  - 线程安全
---

# java web中的线程安全问题
## 问题描述:
### 伪代码:

	判断redis是否有相应的键值对:
		有:
			读取redis
		没有:
			读取数据库
			插入redis(列表的插入)
	返回
### 问题
当请求并发时,第一个请求redis未插入而第二个请求也进入了判断,此时第二个请求的判断条件也是"否",所以就会出现redis数据重复的情况.
### 原因
虽然说对于每一个单独的请求,spring都是新建一个单独的线程来进行处理,但是这并不能够解决上面的问题,究其原因,则是访问了同一个全局数据(可以认为是redis)
## 解决:
增加一个全局变量,使用synchronize同步方法,伪代码如下
### 伪代码
	全局变量 lock
	判断redis是否有相应的键值对:
		有:
			读取redis
		没有:
			synchronize(lock)
				再次判断redis是否有相应的键值对
					有:
						读取redis
					没有:
						读取数据库
						插入redis
* 再次判断的原因:加锁只是阻塞了第二个请求的读取数据库/插入redis操作,如果没有再次判断的话,那么当第一个请求释放锁之后第二个请求依旧会再次读取数据库/插入redis,问题依旧存在

## 参考
[http://www.cnblogs.com/doit8791/p/4093808.html](http://www.cnblogs.com/doit8791/p/4093808.html "Spring单例与线程安全小结")
