---
share: "true"
title: centos系统下wordpress的安装
date: 2019-06-16 22:45:52
tags: 
---

## 安装apache,php

```
yum install -y httpd  php php-mysql php-gd php-xml
```

### 启动&重启apache服务

```
service httpd start  //启动
systemctl restart httpd.service  //此命令亦可重启服务
service httpd restart  重启
```

### 相关目录

```
/etc/httpd/conf/httpd.conf   配置文件位置
/var/www/html  前端文件默认根目录
```

## 升级php

### 配置yum源:

```
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
```

### 卸载旧版本的php

```
rpm -e `rpm -qa|grep php`
```

### 安装新版本php:

```
yum install -y php72w php72w-mysql php72w-gd php72w-ldap php72w-odbc php72w-pear php72w-xml php72w-xmlrpc php72w-mbstring php72w-snmp
```

### 查看httpd是否加载了PHP7模块

```
ll /etc/httpd/modules/|grep php
```

### 查看php版本

```
php -v
```

## 安装mysql

```
yum localinstall http://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
yum install mysql-community-server
service mysqld start

//查看mysql的root账号的密码
grep 'temporary password' /var/log/mysqld.log

//登录mysql
mysql -uroot -p

//修改密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password';
```

### 启动&重启命令

```
service mysqld start
```

### 创建wordpress数据库

```
//登陆数据库
mysql –uroot –p
//创建数据库
create database wordpress;
```

## 配置wordpress

### 下载文件

```
访问网址 https://rup.wordpress.org/download/
进入目录 /var/www/html
下载文件 wget 
unzip  解压下载的文件
```

### 配置

```
cp wp-config-sample.php  wp-config.php 
```

修改文件相关配置

* 数据库相关配置(库名,用户名,密码,连接地址)
* 设置语言包  `define('WPLANG', 'zh_CN');`
* 设置ftp连接解决方式  `define('FS_METHOD','direct');`

修改文件夹权限

```
chmod -R  777 ./wordpress
```

### 访问网址

```
http://ip/wordpress
```

## 站点配置调整

修改apache配置使得访问网站时输入ip地址会自动跳转到  http://ip/wordpress (补充后面的子目录,不需要手动输入)

### 修改配置文件

```
vim /etc/httpd/conf/httpd.conf
添加如下
RewriteEngine on
RewriteCond  %{REQUEST_URI} ^/$
RewriteRule  (.*) http://%{SERVER_NAME}/wordpress/ [L,R=301]
```

重启apache

```
service httpd restart  重启
```

## 参考

[Centos 7 搭建 WordPress](https://juejin.im/entry/5ab34eb76fb9a028cc61202e)

[WordPress yum升级PHP版本到PHP7.2](https://www.tracymc.cn/archives/499)

[wordpress切换语言/语言包](https://cn.wordpress.org/switching/)

[WordPress 安装主题、插件、更新时需要FTP的解决办法](https://www.wpcom.cn/tutorial/101.html)

[apache2重定向至子目录](https://www.jianshu.com/p/57e868e4a1fa)
