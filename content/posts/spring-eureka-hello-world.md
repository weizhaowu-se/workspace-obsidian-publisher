---
share: "true"
title: spring-eureka-hello-world
date: 2020-04-09 22:22:16
tags:
  - SpringBoot
  - SpringCloud
---

# spring-eureka-hello-world

* 核心概念

```
spring-eureka：注册中心，实现服务注册和发现
```

* Hello world代码地址 [[spring-eureka](https://github.com/weizhaowu-se/spring-eureka)](https://github.com/weizhaowu-se/spring-eureka)

### 注册中心server

1. 初始化springboot（此处略去）
2. 配置相关依赖

```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-eureka-server</artifactId>
   <version>1.4.7.RELEASE</version>
</dependency>
```

<!--more-->

3. 在Application启动类上增加注解 `EnableEurekaServer`

```
@SpringBootApplication
@EnableEurekaServer
public class SpringEurekaServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringEurekaServerApplication.class, args);
	}

}
```

4. application.properties增加如下配置项

```
server.port=8080
eureka.instance.hostname=localhost
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
eureka.client.service-url.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka

```

* 各配置项的含义具体可以参见  `https://blog.csdn.net/u010109732/article/details/79769488`

5. 启动程序后，访问页面 `http://127.0.0.1:8080/`,可以看到如下页面

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdnw6a2kcbj31gi0nvgop.jpg)

### 服务提供者client

1. 初始化springboot（此处略去）
2. 配置相关依赖

```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-eureka-server</artifactId>
   <version>1.4.7.RELEASE</version>
</dependency>
```

3. 在Application启动类上增加注解`EnableDiscoveryClient`

```
@SpringBootApplication
@EnableDiscoveryClient
public class SpringEurekaClientApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringEurekaClientApplication.class, args);
	}

}
```

4. 新增测试接口类

```
@RestController
public class HelloController {
	@RequestMapping(value = "/hello", method = RequestMethod.GET)
	public String hello() {
		return "hello springeurekaclient";
	}
}
```

5. 修改application.properties配置文件

```
server.port=8090

spring.application.name=SERVICE-HELLO

eureka.client.service-url.defaultZone=http://localhost:8080/eureka
```

* 配置项比较易懂，其中`eureka.client.service-url.defaultZone`为上一步的注册中心的地址，对应注册中心配置项中的`url.defaultZone`

6. 启动程序后，先访问自身接口`http://127.0.0.1:8090/hello`

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdnwbken1ij30hf03st8p.jpg)

7. 访问注册中心server `http://127.0.0.1:8080/`

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdnwcyirlmj31ew0m30wh.jpg)

### 服务消费者consumer

1. 初始化springboot（此处略去）
2. 配置相关依赖

```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-eureka-server</artifactId>
   <version>1.4.7.RELEASE</version>
</dependency>
```

3. 在Application启动类上增加注解`EnableDiscoveryClient` & 添加Bean注册

```
@SpringBootApplication
@EnableDiscoveryClient
public class SpringEurekaConsumerApplication {

   @Bean
   @LoadBalanced
   RestTemplate restTemplate() {
      return new RestTemplate();
   }

   public static void main(String[] args) {
      SpringApplication.run(SpringEurekaConsumerApplication.class, args);
   }

}
```

4. 修改application.properties配置文件

```
server.port=8091

spring.application.name=consumer-hello

eureka.client.service-url.defaultZone=http://localhost:8080/eureka
```

5. 新增测试接口类，通过注册中心调用上一步的client接口

```
@RestController
public class HelloConsumerController {
   @Autowired
   RestTemplate restTemplate;

   @RequestMapping(value = "/helloConsumer", method = RequestMethod.GET)
   public String helloConsumer() {
      return restTemplate.getForEntity("http://SERVICE-HELLO/hello", String.class).getBody();
   }
}
```

6. 先访问注册中心页面  `http://127.0.0.1:8080/`

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdnwjxlt4hj31ci0jrjux.jpg)

7. 访问页面接口 `http://127.0.0.1:8091/helloConsumer`

![](https://tva1.sinaimg.cn/large/00831rSTgy1gdnwko4da5j30k705xwek.jpg)

可以看到，我们已经调用到了上一步client提供的接口

### 参考

* [Spring Cloud入门教程(一)：服务治理(Eureka)](https://www.jianshu.com/p/d32ae141f680)
