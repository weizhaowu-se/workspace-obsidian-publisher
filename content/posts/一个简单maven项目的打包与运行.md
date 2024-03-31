---
title: 一个简单maven项目的打包与运行
date: 2019-03-24 19:30:39
tags: 
share: "true"
---

# 一个简单maven项目的打包与运行

标题描述的不是很准确,应该说,一个包含外部依赖包的maven包的项目的打包以及运行.

## 创建maven工程

目录结构如下

![](https://ws2.sinaimg.cn/large/006tKfTcgy1g1e4ao4fpej30mm0j476k.jpg)

## 添加依赖包

此处以fastjson为例,在pom.xml里添加依赖如下

```
	<dependencies>
		<!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>fastjson</artifactId>
			<version>1.1.15</version>
		</dependency>

	</dependencies>
```

## 编写demo

```
public class TestPackMain {
	public static void main(String[] args) {
		Map map = new HashMap();
		map.put("a", "b");
		System.out.println(JSON.toJSONString(map));
	}
}
```

## 打包

1. 在pom.xml里添加如下打包配置

此处主要配置两点

* jar包默认运行的main函数
* 编译时的java版本

```
<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-assembly-plugin</artifactId>
				<version>2.5.5</version>
				<configuration>
					<archive>
						<manifest>
							<mainClass>pack.TestPackMain</mainClass>
						</manifest>
					</archive>
					<descriptorRefs>
						<descriptorRef>jar-with-dependencies</descriptorRef>
					</descriptorRefs>
				</configuration>
			</plugin>

			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
```

2. 执行打包命令

```
package assembly:single
```

3. 运行jar包

```
java -jar maven-package-demo-1.0-SNAPSHOT-jar-with-dependencies.jar 
```

控制台输出

```
{"a":"b"}
```

## 如果依赖的是内部的包,怎么办?

//todo
