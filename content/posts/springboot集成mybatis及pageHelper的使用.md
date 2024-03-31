---
share: "true"
title: springboot集成mybatis及pageHelper的使用
date: 2020-03-02 22:23:02
tags:
  - springboot
---

# springboot集成mybatis及pageHelper的使用

## 简单集成

### 新增maven依赖

```
<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>

		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.1.1</version>
		</dependency>
```

<!--more-->

### application.properties配置

前置条件：本地安装数据库

```
spring.datasource.driverClassName = com.mysql.jdbc.Driver
spring.datasource.url = jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8
spring.datasource.username = root
spring.datasource.password = password
```

### mapper代码

```
@Mapper
public interface TestMapper {
	@Select("select * from user")
	List<Map> testSelect();
}
```

### controller代码

注：此处为了简单，直接在controller注入mapper进行方法调用，实际项目中不推荐这样做

```
@Controller
public class HelloController {
   @Autowired
   TestMapper testMapper;

   @RequestMapping(value="/testMybatisSelect",method = RequestMethod.GET)
   @ResponseBody
   public String testMybatisSelect(HttpServletRequest request, HttpServletResponse response) throws IOException {
      return  JSON.toJSONString(testMapper.testSelect());
   }
}
```

### 访问接口

url: `http://localhost:5050/test/testMybatisSelect`

查看结果

![](https://tva1.sinaimg.cn/large/00831rSTgy1gcfybfs9onj30gv04mmxa.jpg)

访问成功

## PageHelper的使用

简单的说，就是实现分页的功能

* [[Mybatis-PageHelper](https://github.com/pagehelper/Mybatis-PageHelper)](https://github.com/pagehelper/Mybatis-PageHelper)

### 新增maven依赖

```
<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.2.1</version>
		</dependency>

		<!-- 分页 -->
		<dependency>
			<groupId>com.github.pagehelper</groupId>
			<artifactId>pagehelper-spring-boot-starter</artifactId>
			<version>1.2.5</version>
		</dependency>
```

### 新增PageResult类

```
public class PageResult<T> {
	private Long totalCount;

	private List<T> content;

	public PageResult() {
	}

	public PageResult(Long totalCount, List<T> datas) {
		this.totalCount = totalCount;
		this.content = datas;
	}

	public Long getTotalCount() {
		return totalCount;
	}

	public void setTotalCount(Long totalCount) {
		this.totalCount = totalCount;
	}

	public List<T> getContent() {
		return content;
	}

	public void setContent(List<T> content) {
		this.content = content;
	}
}

```

### 修改查询方法

```
@RequestMapping(value="/testMybatisSelect",method = RequestMethod.GET)
	@ResponseBody
	public String testMybatisSelect(@Param("pageNum")Integer pageNum, @Param("pageSize")Integer pageSize) throws IOException {
		PageHelper.startPage(pageNum, pageSize);
		Page<Map> page = testMapper.testSelect();
		PageResult pageResult = new PageResult(page.getTotal(), new ArrayList(page));
		return  JSON.toJSONString(pageResult);
	}
```

访问url `http://localhost:5050/test/testMybatisSelect?pageNum=1&pageSize=2`

![](https://tva1.sinaimg.cn/large/00831rSTgy1gcfz4aj2g3j30nh04ct90.jpg)

### 注意

* pageNum是从1开始，当然你从0开始也是可以的，只是0和1是查询到的结果是一样的，个人理解可能只是做了一个容错？
* 关于pageHelper的实现也是一个蛮有趣的话题，后面可以翻下源码看下具体实现
