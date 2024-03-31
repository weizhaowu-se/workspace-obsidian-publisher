---
share: "true"
title: Centos设置定时任务及查看执行日志
date: 2022-10-08 22:52:22
tags: 
---

# 背景

- 服务器（centos）部署了nginx + hexo的组合
- 本地windows编写hexo博客
- hexo博客源数据托管在github上

想要实现效果：

* 本地新增博客源文件（md文件），提交到github上
* centos服务器能够定时拉取代码并更新nginx网站文件

# 具体流程

此处略去配置github之ssh秘钥等步骤

## 执行脚本编写

在目录 ` /etc/cron.daily/`新增文件 `hexoUpdate.sh`即可（注意添加可执行权限 chmod +x hexoUpdate.sh）

```shell
#!/bin/bash
cd /root/new-hexo  && git pull && hexo g -d
```

* 此处目录`/root/new-hexo` 修改为对应的hexo主目录

关于`cron.daily`文件夹下的脚本及执行时间说明可自行上网查看。

## 执行日志查看

```shell
vi /var/log/cron
```

## 延伸

可以通过Git的hook实现提交代码之后自动执行脚本

