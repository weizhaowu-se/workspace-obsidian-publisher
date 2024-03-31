---
share: "true"
title: session与cookie
date: 2018-02-07 13:42:52
tags:
  - session
  - cookie
  - java
  - java web
  - web
---
# session与cookie
## cookie
* 在服务端中设置,在前端中需要看情况
	* 当设置了httponly时,仅能在服务端设置,js端既不能读取也不能够设置.
	* 无设置httponly时,前端也可以设置和读取cookie的值

<!--more-->
## session
* 需要注意的是,session指的是"对话"

		req.getSession().setAttribute("session", "value");
* 按照如上的代码,

		req.getSession().getAttribute("session")
* 就可以获得value值
* 可以想象成服务器维持了一个map,map的key是sessionID,而值则是一个个的键值对,而sessionID的传递方式则是通过cookie来实现的(在初始化session时会生成并且设置cookie,而之后cookie的提交则会带上sessionID)
* 除此之外,在禁用cookie的情况下,还能通过url重写的方式来实现sessionID的传递
* 在分布式部署的情况下,session的同步是一个问题,比较简单的解决方式是使用spring session+redis实现.



