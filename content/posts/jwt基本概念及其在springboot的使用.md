---
share: "true"
title: jwt基本概念及其在springboot的使用
date: 2020-03-01 10:45:45
tags: 
---

# jwt基本概念及其在springboot的使用

## jwt概念

全称：json web token

* [JSON Web Token 入门教程](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)

我的理解

1. 与传统的session认证方式最显著的区别在于，jwt将用户信息存放在客户端，每次请求服务时再将此部分信息传给服务器（一般是通过header参数传递的方式）
2. jwt主要分为三个部分：
   * header：标识签名的算法等属性
   * payload：标识该jwt token的有效时间，签发人等信息，辨识调用方
   * signature：对前两者的签名，避免被篡改

<!--more-->

一个jwt的流程如下

![jwt登录流程图](https://tva1.sinaimg.cn/large/00831rSTgy1gce8yo9ouwj30fb0f10tm.jpg)

## springboot中的使用

参考： 

* [Spring Boot Security 整合 JWT 实现 无状态的分布式API接口](https://juejin.im/post/5ca162b36fb9a05e181de126)

* [GitHub-springboot-jwt](https://github.com/gf-huanchupk/SpringBootLearning/tree/master/springboot-jwt)

  在spring security的基础上新增token的校验，关于spring security后续补充，此处说明下修改的地方，主要有如下几个地方：

* 登录接口新增返回  jwttoken
* 新增过滤器 JwtTokenFilter 对请求header参数的token进行校验
* 修改http session状态为无状态

SecurityConfig中修改：

```
@Override
	protected void configure(HttpSecurity http) throws Exception {

		http.csrf().disable()
				//因为使用JWT，所以不需要HttpSession
				.sessionManagement().sessionCreationPolicy( SessionCreationPolicy.STATELESS).and()
				.authorizeRequests()
				.antMatchers( HttpMethod.OPTIONS, "/**").permitAll()
				.antMatchers("/auth/login").permitAll()
				.anyRequest().authenticated();

		//使用自定义的jwt Token过滤器 验证请求的Token是否合法
		http.addFilterBefore(authenticationTokenFilterBean(), UsernamePasswordAuthenticationFilter.class);
		http.headers().cacheControl();
	}
	
	@Bean
	public JwtTokenFilter authenticationTokenFilterBean() throws Exception {
		return new JwtTokenFilter();
	}
```

## 我理解的使用场景

* 正因为jwt的无状态的特性，那么对于服务器的横向扩展是十分方便的，但是同时也带来了一个问题，token签发之后一直到有效期结束之前都是有效的，所以无法实现类似A地登录B地退出，修改密码后所有客户端退出的功能，除非通过黑名单list的方式进行维护，通过黑名单的方式，借助于外界统一的存储介质（比如数据库或者redis），那么似乎就与jwt的无状态的特性相悖
* 基于以上的理解，jwt更适用于一些应用间的接口鉴权，而非web应用前后端的登录校验

## 参考资料

* [JSON Web Token 入门教程](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)
* [Spring Boot Security 整合 JWT 实现 无状态的分布式API接口](https://juejin.im/post/5ca162b36fb9a05e181de126)
* [讲真，别再使用JWT了！](https://juejin.im/entry/5993a030f265da24941202c2)


