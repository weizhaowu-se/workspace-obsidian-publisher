---
share: "true"
title: Maven实战读书笔记
date: 2017-10-11 20:59:19
tags:
  - Maven
---

# Maven实战

## 常用命令

* mvn clean compile
* mvn clean test
* mvn clean package
  * 默认打包为jar,可以在pom.xml里面指定打包类型
* mvn clean install
  * 将项目打包后安装到本地仓库以供其他项目使用
* 默认打包生成的jar时不能够直接运行的,为了生成可执行的jar文件,需要借助maven-shade-plugin

<!--more-->


