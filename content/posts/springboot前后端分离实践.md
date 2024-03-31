---
share: "true"
title: springboot前后端分离实践
date: 2019-03-19 22:56:51
tags: 
---

# springboot前后端分离实践

## 前言

现在很多项目都是采用springboot+react或者springboot+angularJs的模式进行开发,相对应的部署方式我认为主要有两种

1. 打包后将前端静态文件放在resource下的文件夹里面
2. 利用nginx的反向代理,将前端文件与后台程序分开部署.

而这两者中,我认为后者相对来说更好一些.

## 部署

**要点:**nginx反向代理

### 安装nginx

```
yum install nginx
```

* 常用命令如下

```
nginx 
nginx -s stop
nginx -s reload
nginx -t
```

* 默认配置文件位置

```
/etc/nginx/nginx.conf
```

### 配置nginx

**重要:**配置文件位置`/etc/nginx/nginx.conf`

#### 配置前端文件目录

在对应的server里配置

```
root         /usr/share/nginx/html
```

备注:默认为此,不需要设置

#### 配置反向代理

在对应的server里配置

```
location /api/ {
              proxy_pass http://localhost:8083/api/;
 }  
```

备注:此处的localhost:8083为springboot项目启动的机器ip和端口,也就是说前后端不需要部署在同一台机器上.

### 部署springboot

```
将springboot程序包部署启动
```

### 后记

1. 很多人可能会有疑问,为什么要用反向代理呢?其实原因很简单:跨域.如果仅仅是将nginx作为前端的容器,然后由前端去直接请求springboot后台接口的话,是会有跨域问题的,这些请求是会被浏览器拦截的.
2. 运用nginx的负载均衡,更进一步的话,我们可以在反向代理的时候通过nginx的upstream功能来实现负载均衡,可以指定轮询的策略.


