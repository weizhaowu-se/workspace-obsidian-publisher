---
share: "true"
title: nginx之header转发问题
date: 2020-02-06 10:04:27
tags: 
---

# nginx之header转发问题

项目中的后端一般是通过nginx将请求转发到后端的springboot中，但是在使用的过程中发现，当header的key值包含下划线时，后端的controller无法获取到该header，也就是说，带下划线key的header在经过nginx转发后丢了。

## 简单验证

### 后端接口代码

```
@RequestMapping(value="/getHeaders",method = RequestMethod.GET)
	@ResponseBody
	public String getHeader(HttpServletRequest request){
		Map headerMap = new HashMap();

		Enumeration names = request.getHeaderNames();
		while (names.hasMoreElements()) {
			String name = names.nextElement().toString();
			String value = request.getHeader(name);
			headerMap.put(name, value);
		}

		return JSON.toJSONString(headerMap, true);
	}
```

<!--more-->

## 使用postman请求

在请求上添加header

```
test:test
test-test:test
```

* 不经过nginx转发时,正常接收

```
{
	"test": "test",   
	"cookie": "UserId=320322197306158953; JSESSIONID=5854303E447E67566499FC29AF43E490",
	"postman-token": "a5f96140-d3a1-4b1a-b91a-29587ffdc7cc",
	"test_test": "test",
	"host": "localhost:8083",
	"connection": "keep-alive",
	"cache-control": "no-cache",
	"accept-encoding": "gzip, deflate, br",
	"user-agent": "PostmanRuntime/7.22.0",
	"accept": "*/*"
}
```

* 经过nginx转发时，丢失带下划线的header

```
{
	"test":"test",
	"cookie":"UserId=320322197306158953; JSESSIONID=5854303E447E67566499FC29AF43E490",
	"postman-token":"65b22f5b-790c-4ca1-99de-f31c81777a94",
	"host":"localhost:8083",
	"connection":"close",
	"cache-control":"no-cache",
	"accept-encoding":"gzip, deflate, br",
	"user-agent":"PostmanRuntime/7.22.0",
	"accept":"*/*"
}
```

## 解决方法

* 修改header命名方式
* 修改nginx配置，http中增加配置

```
underscores_in_headers on;
```


