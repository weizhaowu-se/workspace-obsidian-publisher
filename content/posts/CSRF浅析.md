---
share: "true"
title: CSRF浅析
date: 2018-01-24 11:01:37
tags:
  - web安全
  - CSRF
---

# CSRF浅析
## 什么是CSRF
Cross-site request forgery,跨站请求伪造.
## 简单的攻击样例
1. 小明访问并且登录了网站A
2. 小明访问坏人的网站B,网站B上存在一些网站A的请求(GET或者POST)
		
		一个简单的get请求可以利用img的src属性:
		<img src="http://example.com/get?xxx=xxx">
3. 访问时触发了请求,此时由于请求中携带着相关的cookie信息,所以网站A是会通过这些非法的请求的,也就是跨站(在黑客网站B)请求伪造.

## 预防
### referer验证
这是一种不安全的校验方法
#### 校验原理
对于网络请求,在header里面存在着referer属性,这个属性标识出请求的来源网站,所以我们可以通过验证referer来预防csrf攻击
#### 存在问题
1. 对于某些浏览器来说,这个属性可能为空
2. referer可以轻易地伪造
3. 从搜索引擎跳转时,referer是搜索引擎,所以可能需要维护一个白名单

### 双提交cookie
较为安全
#### 实现
对于每一个网络请求,在提交请求时,在js中将cookie中的某些值(可以是单独生成的token或者用户标志灯)添加到header上提交,在后台去校验是否存在token以及是否与cookie的值相匹配
#### 原理
当你在访问坏人的网站B时,同时提交的仅仅只有cookie,而你在正常访问时,提交时会在header中添加csrftoken.
#### 问题
其实也不能算是问题吧,如果你的网络请求是统一到一个方法里面执行的,例如说ajax或者dwr,那么就可以很方便地修改,但是如果不是的话,那么修改的工作量可能就有点大了.


## 参考
[https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%AB%99%E8%AF%B7%E6%B1%82%E4%BC%AA%E9%80%A0 "跨站请求伪造-维基百科")
[https://zh.wikipedia.org/wiki/HTTP%E5%8F%83%E7%85%A7%E4%BD%8D%E5%9D%80](https://zh.wikipedia.org/wiki/HTTP%E5%8F%83%E7%85%A7%E4%BD%8D%E5%9D%80 "HTTP来源地址-维基百科")
[https://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html](https://www.cnblogs.com/hyddd/archive/2009/04/09/1432744.html "浅谈CSRF攻击方式")

