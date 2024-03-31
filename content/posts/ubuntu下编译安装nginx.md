---
share: "true"
title: ubuntu下编译安装nginx
date: 2017-04-13 14:14:12
tags:
  - ubuntu
  - vps
  - nginx
---

# Ubuntu下编译安装nginx

* 参考[这里](http://www.cnblogs.com/xiongmaolinux/p/5345117.html)

* 安装完成之后，编辑修改nginx.conf， 路径是
		/usr/local/nginx/conf/nginx.conf

* 启动nginx 
		sudo /usr/local/nginx/sbin/nginx	

* 关闭nginx
	    sudo /usr/local/nginx/sbin/nginx -s stop

* 重读nginx配置文件
    	sudo /usr/local/nginx/sbin/nginx -s reload
