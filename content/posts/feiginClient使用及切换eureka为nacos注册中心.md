---
share: "true"
title: feignClient使用及切换eureka为nacos注册中心
date: 2020-04-28 22:42:37
tags:
  - springbo
  - 微服务
---

此文接着上篇内容 [spring-eureka-hello-world](https://image-shanghai-1258937892.cos.ap-shanghai.myqcloud.com/2020/04/09/spring-eureka-hello-world/)，代码仓库地址： https://github.com/weizhaowu-se/spring-eureka 分支： master-nacos

<!--more-->

## feignClient使用

上次通过客户端调用服务方时，采用的是restTemplate的方式，代码如下

```
  @Autowired
	RestTemplate restTemplate;
	
	@RequestMapping(value = "/helloConsumer", method = GET)
	public String helloConsumer() {
		return restTemplate.getForEntity("http://SERVICE-HELLO/hello", String.class).getBody();
	}
```

始终是有点丑陋，我们现在通过 `FeignClient` 的方式来进行RPC调用。

1. 引入依赖包

```
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-feign</artifactId>
			<version>1.4.7.RELEASE</version>
		</dependency>
```

2. 在MainApplication配置注解 	@EnableFeignClients

3. 新建接口如下

```
@FeignClient(value = "SERVICE-HELLO")
public interface HelloService {
	@RequestMapping(value = "/hello", method = RequestMethod.GET)
	String hello();
}
```

* 可以看到，类上面的FeignClient 对应的value为调用的服务名，而方法上的value则是对应具体的地址

4. 在Controller中新增通过FeignClient调用的接口方法

```
@Autowired
	HelloService helloService;

@RequestMapping(value = "/helloConsumerFeignClient", method = GET)
	public String helloConsumerFeignClient() {
		return helloService.hello();
	}
```

5. 重启应用，访问接口 `http://localhost:8091/helloConsumerFeignClient`,访问成功

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ge9vfkpduvj30gr03y74c.jpg)

## 切换为nacos注册中心

上面我们使用的是eureka作为服务注册中心，现在我们修改为通过nacos实现服务注册与服务发现

1. 启动本地nacos，参考地址  `https://nacos.io/zh-cn/docs/quick-start.html`,启动命令为

```
sh startup.sh -m standalone
```

* 访问 `http://localhost:8848/nacos`,账号密码  nacos/nacos

2. 修改服务提供方

* 添加nacos依赖，注释eureka依赖

```
		<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
		<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
			<version>1.5.1.RELEASE</version>
		</dependency>
		<!--去除eureka依赖-->
				<!--<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
			<version>1.4.7.RELEASE</version>
		</dependency>-->
```

* 增加nacos服务器配置地址，去除eureka配置地址

```
#eureka.client.service-url.defaultZone=http://localhost:8080/eureka

spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
```

* 重启应用，访问 `http://localhost:8848/nacos`，可以看到对应的服务

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ge9vmx5hwtj31fx0ckgny.jpg)

* 添加nacos依赖，注释eureka依赖

```
		<!-- https://mvnrepository.com/artifact/com.alibaba.cloud/spring-cloud-starter-alibaba-nacos-discovery -->
		<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
			<version>1.5.1.RELEASE</version>
		</dependency>
		
				<!--<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
			<version>1.4.7.RELEASE</version>
		</dependency>-->
```

* 增加nacos服务器配置地址，去除eureka配置地址

```
#eureka.client.service-url.defaultZone=http://localhost:8080/eureka

spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
```

* 重启应用，访问接口  `http://localhost:8091/helloConsumerFeignClient` 或者 `http://localhost:8091/helloConsumer`

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ge9vr1gjlaj30eo03ut8q.jpg)

* 至此，我们已经成功将eureka切换为nacos

### 参考

1. [nacos快速开始](https://nacos.io/zh-cn/docs/quick-start.html)
2.   [nacos-spring-cloud](https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html)
