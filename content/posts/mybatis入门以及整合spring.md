---
share: "true"
title: mybatis入门以及整合spring
date: 2017-08-23 20:39:27
tags:
  - mybatis
  - spring
---
# mybatis入门
## 什么是mybatis
* MyBatis是一个Java持久化框架，它通过XML描述符或注解把对象与存储过程或SQL语句关联起来(from维基百科)
## 准备工作
* mybatis的jar包
* 一个数据库(我采用的是mysql)
## 具体如下
### 数据库结构如下
![](http://7xkzud.com1.z0.glb.clouddn.com/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20170823210127.png)
<!--more-->
### my-batis文件
* 配置文件,配置连接的数据库,账户名,密码等,同时指定使用到的mapper的位置

		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE configuration
		        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
		        "http://mybatis.org/dtd/mybatis-3-config.dtd">
		<configuration>
		    <environments default="development">
		        <environment id="development">
		            <transactionManager type="JDBC"/>
		            <dataSource type="POOLED">
		                <property name="driver" value="com.mysql.jdbc.Driver"/>
		                <property name="url" value="jdbc:mysql://127.0.0.1:3306/db_develop" />
		                <property name="username" value="root"/>
		                <property name="password" value="password"/>
		            </dataSource>
		        </environment>
		    </environments>
		    <mappers>
		        <mapper url="file:///F:/IDEAProject/WebAppLearn/UserMapper.xml"/>
		    </mappers>
		</configuration>
* mapper文件

		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE mapper
		        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
		        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
		<mapper namespace="com.wilbert.mapper.UserMapping">
		    <select id="getUser" resultType="com.wilbert.model.User">
		        select * from tb_user where id = #{id}
		    </select>
		</mapper>
	* 注意其中的namespace,命名空间,可以将其认为是java中的包,而id则为java中具体的类,这样在下面的调用就比较好理解了.
* 数据模型类,一个普通的java类,拥有getter,setter方法.
![](http://7xkzud.com1.z0.glb.clouddn.com/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20170823211026.png)
* 测试类

		public class MyBatisLearn {
		    public static void main(String[] args) throws IOException {
		        String resource = "src/main/java/com/wilbert/config/mybatis-config.xml";
		        InputStream inputStream = new FileInputStream(resource);
		        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		        SqlSession sqlSession = sqlSessionFactory.openSession();
		        String statement = "com.wilbert.mapper.UserMapping.getUser";
		        User user = sqlSession.selectOne(statement, "5b6f8d5739834dddb785c5e909471e69");
		    }
		}

	* 需要注意的是statement,statement是由namespace+id构成.
* 运行结果

		id:5b6f8d5739834dddb785c5e909471e69,name:a,password:a
### 后续
* 从上面可以看到,其中至关重要的是mapper文件,在mapper文件中我们配置了各式各样的sql,而mybatis也提供了很强大的语法来实现复杂的查询或者插入,更新等.详情可以进一步查看[http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html](http://www.mybatis.org/mybatis-3/zh/sqlmap-xml.html)


### 参考资料:
[http://www.mybatis.org/mybatis-3/zh/getting-started.html](http://www.mybatis.org/mybatis-3/zh/getting-started.html)



