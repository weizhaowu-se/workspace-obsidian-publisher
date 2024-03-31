---
share: "true"
title: mysql-binlog之主从同步
date: 2020-06-17 23:55:08
tags:
  - mysql
---

# mysql-binlog之主从同步

基于上一篇我们已经在本地环境安装了两个mysql，这里我们来进行简单的配置来实现基于binlog的主从复制。

<!--more-->

1. 配置master数据库配置文件my.cnf

```
[mysqld]
## 设置server_id，一般设置为IP,注意要唯一
server_id=100
## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
binlog-ignore-db=mysql
## 开启二进制日志功能，可以随便取，最好有含义（关键就是这里了）
log-bin=edu-mysql-bin
## 为每个session 分配的内存，在事务过程中用来存储二进制日志的缓存
binlog_cache_size=1M
## 主从复制的格式（mixed,statement,row，默认格式是statement）
binlog_format=mixed
## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
expire_logs_days=7
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
```

* 由于mac上通过dmg安装的mysql默认没有这个配置文件，需要在/etc/下手动新建my.cnf 文件并配置如上
* 配置完成后重启mysql

2. master创建用户用来进行同步操作

```
CREATE USER 'slave'@'%' IDENTIFIED BY '123456';

GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
```

3. 配置slave数据库配置文件my.cnf

```
[mysqld]
## 设置server_id，一般设置为IP,注意要唯一
server_id=101
## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
binlog-ignore-db=mysql
## 开启二进制日志功能，以备Slave作为其它Slave的Master时使用
log-bin=edu-mysql-slave1-bin
## 为每个session 分配的内存，在事务过程中用来存储二进制日志的缓存
binlog_cache_size=1M
## 主从复制的格式（mixed,statement,row，默认格式是statement）
binlog_format=mixed
## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
expire_logs_days=7
## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
## relay_log配置中继日志
relay_log=edu-mysql-relay-bin
## log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1
## 防止改变数据(除了特殊的线程)
read_only=1
```

* 由于是通过docker安装的，配置文件的位置和你的启动命令所映射到的本机的目录相关

4. 配置主从同步

master中执行如下

```
show master status;
```

需要记录的内容有

* File
* Position

slave中执行如下

```
change master to master_host='host.docker.internal', master_user='slave', master_password='123456', master_port=3306, master_log_file='edu-mysql-bin.000001', master_log_pos=617, master_connect_retry=30;
```

* master_host：master对应的连接地址，此处填写docker容器访问宿主机的默认地址`host.docker.internal`
* master_log_file: 上面记录的File
* master_log_pos： 上面记录的Position
* 其他账号密码等信息

```
start slave;   ---启动主从复制
stop slave; ---停止主从复制
show slave status;   ---显示slave状态
```

6. 可以尝试建一个表/数据库来验证主从复制的过程。

### 参考

* [MySQL主从复制搭建，基于日志（binlog）](https://www.jianshu.com/p/4876a0aab3e8)
* [解决：Mac下mysql配置文件没有my-default.cnf，无法配置my.cnf](https://www.jianshu.com/p/628bcf8bb557)

* [Docker容器访问宿主机网络](https://jingsam.github.io/2018/10/16/host-in-docker.html)


