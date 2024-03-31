---
share: "true"
title: 使用nacos配置中心
date: 2020-04-29 21:50:16
tags:
  - 微服务
  - nacos
---

# 使用nacos配置中心

前言：此部分的内容基于   [feignClient使用及切换eureka为nacos注册中心](https://image-shanghai-1258937892.cos.ap-shanghai.myqcloud.com/2020/04/28/feiginClient%E4%BD%BF%E7%94%A8%E5%8F%8A%E5%88%87%E6%8D%A2eureka%E4%B8%BAnacos%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83/)，代码地址见  https://github.com/weizhaowu-se/spring-eureka 分支： master-nacos

<!--more-->

1. 添加代码依赖

```
		<!-- https://mvnrepository.com/artifact/com.alibaba.boot/nacos-config-spring-boot-starter -->
		<dependency>
			<groupId>com.alibaba.boot</groupId>
			<artifactId>nacos-config-spring-boot-starter</artifactId>
			<version>0.1.6</version>
		</dependency>
```

2. 在MainApplication上添加注解 `@NacosPropertySource(dataId = "example", autoRefreshed = true)`，注意此处的dataId对应的值，这个是我们在nacos配置平台上发布配置的对应环境
3. 新增测试Controller

```
@RestController
public class ConfigGetController {

	@NacosValue(value = "${testConfig:1234}", autoRefreshed = true)
	private String testConfig;

	@RequestMapping(value = "/get", method = RequestMethod.GET)
	@ResponseBody
	public String get() {
		return testConfig;
	}
}
```

* @NacosValue(value = "${testConfig:1234}", autoRefreshed = true)： 1234表示默认值，autoRefreshed表示默认刷新

4. 测试访问地址 `http://localhost:8091/get`，得到返回  `1234`(由于我们此时还未配置，所以返回默认值)
5. 在nacos配置管理上新增配置项

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geazybr3vcj31ha0c5762.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geazz6bm8vj31gv0ktjsp.jpg)

6. 再次访问`http://localhost:8091/get`，配置生效

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geb00g4ofwj30ha040glk.jpg)


