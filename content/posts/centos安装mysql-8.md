---
share: "true"
title: centos安装mysql-8
date: 2020-10-09 22:39:37
tags: 
---

# centos安装mysql-8

前景说明：通过安装mysql-8，在centos上连接云上的数据库，而非部署mysql服务端

试用场景：云上数据库配置了ip白名单，只能通过centos服务器进行访问的情况

<!--more-->

### 配置Mysql 8.0安装源

```bash
sudo rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
```

### 安装Mysql 8.0

```
sudo yum --enablerepo=mysql80-community install mysql-community-server
```

至此，安装完成

* 客户端版本5.7 + 服务端版本8.0的情况，直接连接会报错

```
Authentication plugin 'caching_sha2_password' cannot be loaded
```

* 可以参考  [MySQL 8.0报错：error 2059: Authentication plugin 'caching_sha2_password' cannot be loaded](https://blog.csdn.net/vkingnew/article/details/80105323)做相应修改，但是我在此处直接重新安装了8.0版本的mysql

### 参考

* [CentOS 7 安装 Mysql 8.0 教程](https://blog.csdn.net/danykk/article/details/80137223)

* [MySQL 8.0报错：error 2059: Authentication plugin 'caching_sha2_password' cannot be loaded](https://blog.csdn.net/vkingnew/article/details/80105323)


