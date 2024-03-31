---
share: "true"
title: springboot数据库与mybatis
date: 2019-03-18 21:01:35
tags:
  - java
  - springboot
---

# springboot数据库与mybatis入门配置

## 初始化工程

参考链接:`https://blog.csdn.net/typa01_kk/article/details/76696618`

## 数据库配置以及测试

在application.properties配置如下

```
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.max-idle=10
spring.datasource.max-wait=10000
spring.datasource.min-idle=5
spring.datasource.initial-size=5
```

通过jdbcTemplate的方式访问数据库

```
@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoSpringbootApplicationTests {
	@Autowired
	JdbcTemplate jdbcTemplate;

	@Test
	public void contextLoads() {
		String sql = "select * from user";
		List<Map<String, Object>> resultList = jdbcTemplate.queryForList(sql);
		System.out.println("query result:" + JSON.toJSONString(resultList));
		for (Map<String, Object> item: resultList) {
			for (Map.Entry<String, Object> entry: item.entrySet()) {
				System.out.println(entry.getKey() + ":" + entry.getValue().toString());
				System.out.println("\n");
			}
		}
	}
}

```

日志打印如下:

```
query result:[{"id":1,"name":"test"}]
id:1


name:test
```

成功访问数据库.

## mybatis配置

一般来讲,我们都是通过mybatis以及xml的方式访问数据库.

添加dao接口

```
/**
 * @author wuweizhao
 * @version 创建时间：2019/3/18 9:26 PM
 */
@Mapper
public interface UserMapper {
	List<Map> querUser();
}
```

添加mapper.xml文件(文件位置:resources/mapper/UserMapper.xml)

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demospringboot.dao.UserMapper">
	<select id="querUser" resultType="java.util.Map">
		SELECT
		id,
		name
		FROM
		user
	</select>
</mapper>
```

添加配置信息(application.properties)

```
mybatis.mapper-locations=classpath:mapper/**/*.xml
```

编写测试代码

```
@Autowired
	UserMapper userMapper;
	@Test
	public void testMybatis() {
		System.out.println(JSON.toJSONString(userMapper.querUser()));
	}
```

查看控制台打印信息

```
[{"name":"test","id":1}]
```

完整的application.properties配置文件如下

```
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.max-idle=10
spring.datasource.max-wait=10000
spring.datasource.min-idle=5
spring.datasource.initial-size=5

server.port=8083
server.tomcat.uri-encoding=UTF-8

mybatis.mapper-locations=classpath:mapper/**/*.xml
```

完整的pom.xml文件如下

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>demo-springboot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo-springboot</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>fastjson</artifactId>
			<version>1.2.47</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.2.1</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```



## 参考

1. [https://blog.csdn.net/typa01_kk/article/details/76696618](https://blog.csdn.net/typa01_kk/article/details/76696618)
2. [https://blog.csdn.net/zhoujiyu123/article/details/79786847](https://blog.csdn.net/zhoujiyu123/article/details/79786847)


