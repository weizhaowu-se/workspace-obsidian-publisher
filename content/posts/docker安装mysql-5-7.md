---
share: "true"
title: docker安装mysql-5.7
date: 2020-06-17 23:33:58
tags:
  - docker
  - mysql
---

# docker安装mysql-5.7

1. 拉取镜像

```
docker pull mysql:5.7
```

拉取成功

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfvpkx6g6qj31yq04c3zb.jpg)

<!--more-->

2. 创建本地文件夹映射docker目录

```
mkdir -p /Users/wilbert/docker/mysql-5.7/data
mkdir -p /Users/wilbert/docker/mysql-5.7/logs
mkdir -p /Users/wilbert/docker/mysql-5.7/conf
```

创建mysql配置文件my.cnf

```
cd /Users/wilbert/docker/mysql-5.7/conf
touch my.cnf
```

3. 启动mysql

```
docker run -p 4406:3306 --name mysql -v /Users/wilbert/docker/mysql-5.7/conf:/etc/mysql/conf.d -v /Users/wilbert/docker/mysql-5.7/logs:/logs -v /Users/wilbert/docker/mysql-5.7/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7
```

各个命令解释如下

```
--name  为容器指定一个名称
-v      将本地目录映射到docker内部目录中
-p			端口映射
-e      设置参数
```

由于我们已经指定了容器名称，所以我们后续要重启只需要执行如下命令即可

```
docker start mysql
```

测试连接成功

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfvq20irc3j30ty0vyagg.jpg)

### 参考

* [docker 安装 mysql5.7](https://blog.csdn.net/weixin_40461281/article/details/92610876)
* [Docker-端口映射](https://www.jianshu.com/p/b92d4b845ed6)
* [docker run和start的区别](https://www.jianshu.com/p/b3f94f19fd02)
* [docker笔记 - container name 冲突](https://blog.csdn.net/972301/article/details/80915127)
* [Docker run 命令](https://www.runoob.com/docker/docker-run-command.html)


