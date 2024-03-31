---
share: "true"
title: minio时间问题及docker时区修改
date: 2020-09-01 21:24:13
tags: 
---

# minio时间问题及docker时区修改

## 问题描述

 The difference between the request time and the current time is too large

## 问题背景

* 通过docker部署的minio
* 通过docker部署的springboot应用
* centos - 7.5

## 解决方式

* 修改容器的时区
  * minio是通过`docker run`命令启动的，所以只需要添加下启动参数`-v /etc/localtime:/etc/localtime`即可（这一步是将容器的时区和宿主机的时区设置为一致）
  * springboot是通过DockerFile打包启动的，在DockerFile中添加`RUN ln -sf /usr/share/zoneinfo/Asia/ShangHai /etc/localtime`即可
  * 在修改完时区之后，可以通过命令` date -R`查看具体时间和时区
* 注意，在时区一致的情况下，如果宿主机的时间有错的话，也会导致不能够解决问题，这个时候我们需要修改宿主机的时间，有两种方式进行修改
  * `date -s '2020-09-01 00:00:00'`
  * 或者通过其他在线的时钟同步方式进行时间同步


