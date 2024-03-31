---
share: "true"
title: sprintboot&docker&HelloWorld
date: 2020-04-19 16:05:14
tags:
  - springboot
  - docker
---

# sprintboot&docker&HelloWorld

## 初始化springboot项目

1. 初始化springboot项目
2. 新建HelloController

```
@RestController
public class HelloController {
	@RequestMapping(value = "/", method = RequestMethod.GET)
	public String hello() {
		return "hello docker demo";
	}
}

```

<!--more-->

## docker相关依赖及文件配置

### 新增依赖

pom.xml里面添加如下

```
<properties>
		<docker.image.prefix>springio</docker.image.prefix>
</properties>

<build>
		<plugins>
			<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>1.0.0</version>
				<configuration>
					<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
					<dockerDirectory>src/main/docker</dockerDirectory>
					<resources>
						<resource>
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>
				</configuration>
			</plugin>
		</plugins>
	</build>
```

### Dockerfile

新建文件 src/main/docker/Dockerfile

```
FROM openjdk:8-jdk-alpine
VOLUME /tmp
ADD docker-domer-0.0.1-SNAPSHOT.jar app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

```

### 打包镜像

```
mvn package docker:build
```

命令执行完成后，查看本地docker镜像列表

```
docker images
```

可以看到

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
springio/docker-domer                           latest              37c1337c6595        3 minutes ago       122MB
```

执行命令启动镜像

```
docker run -i -t -p 8080:8080  springio/docker-domer:latest
```

访问HelloController测试接口

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gdz5ff3f0tj30in05faa1.jpg)

至此，已经完成了springboot镜像的打包同启动

代码地址：[springboot-docker-demo](https://github.com/weizhaowu-se/springboot-docker-demo)
