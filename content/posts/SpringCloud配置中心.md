---
share: "true"
title: SpringCloud配置中心
date: 2020-04-06 15:39:44
tags: 
---

# SpringCloud配置中心

## 服务端

[spring-config-server完整代码地址](https://github.com/weizhaowu-se/spring-config-server)

1. 初始化springboot项目
2. 新增配置中心依赖

```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
			<version>2.0.1.RELEASE</version>
		</dependency>

```

3. 本地配置的方式

   1. 修改`application.properties`

   ```
   spring.application.name=service-config
   spring.profiles.active=native
   #指定配置文件位置为classpath目录下的config目录
   spring.cloud.config.server.native.searchLocations=classpath:/config
   ```

   <!--more-->

   2. 在resource目录下新建config及新建配置文件

   ```
   resource
   	config
   		service-dev.properties
   		service-prod.properties
   ```

   * 此处的  `service` 为对应的服务名称，可自定义， `-`后面的为指定的环境，可自定义
   * service-dev.properties 文本内容

   ```
   env.name=dev  
   env.password=dev123456 
   ```

   * service-prod.properties 文本内容

   ```
   env.name=product  
   env.password=product123456 
   ```

4. git远程仓库配置方式   ---todo

5. 经过了4/5的步骤之后，启动config-server项目，可以从浏览器访问可以查看到对应的配置信息，如下

   1. 访问 `http://localhost:8080/service/dev`获得service-dev配置

   ![](https://tva1.sinaimg.cn/large/00831rSTgy1gdk3jwnf8mj30ff0a3mxs.jpg)

   2. 访问 `http://localhost:8080/service/prod` 获得 service-prod配置

   ![](https://tva1.sinaimg.cn/large/00831rSTgy1gdk3kt183sj30js0a8mxw.jpg)

## 客户端

[spring-config-client完整代码地址](https://github.com/weizhaowu-se/spring-config-client)

1. 初始化springboot项目
2. 新增config依赖配置

```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
			<version>2.0.1.RELEASE</version>
		</dependency>
```

3. `resource` 目录下新增文件 `bootstrap.properties`

```
# 对应服务端的某个配置服务
spring.application.name=service
# 配置中心地址
spring.cloud.config.uri=http://127.0.0.1:8080
# 启用的配置中心的某个配置文件
spring.cloud.config.profile=prod
```

4. 新增 `HelloController`进行验证

```
@RestController
public class HelloController {
	@Value("${env.password}")
	private String password;

	@RequestMapping(value = "/test", method = RequestMethod.GET)
	public String testProfiles() {
		return password;
	}
}
```

5. 访问上一步新增的接口

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdk42g3r8bj30e10310so.jpg)

## 配置刷新  todo

## 注册中心eurka todo

https://www.jianshu.com/p/d32ae141f680

## TIPS

* 注意SpringBoot和SpringCloud的版本对应关系，否则可能会启动失败
