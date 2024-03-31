---
share: "true"
title: vps+git+hexo+nginx搭建博客网站
date: 2017-04-13 12:52:29
tags:
  - vps
  - git
  - hexo
  - nginx
  - blog
---
# vps+git+hexo+nginx搭建博客网站
由于科学上网的需要，买了一个vps，想着单单用来ss似乎不太值，所以就想说搭建一个简单的博客系统，wordpress比较繁杂，所以就采用了这样的一个方式来搭建博客：git+hexo（主题：next）+nginx，实现思路大概是这样子的：
- 在本地windows上搭建hexo，编写博客之后hexo generate，生成html文件
- 在vps上搭建git服务器，nginx服务
- 将本地html文件更新到服务器上
- 使用git hook功能将服务器git目录更新到网页文件上
<!--more-->
## windows上搭建hexo
[官方文档](https://hexo.io/zh-cn/docs/index.html)
![](http://i2.muimg.com/567571/05606b0fdd51bb41.png)
安装完成。
## vps安装git服务
### 安装git
        sudo apt-get install git
### 创建git用户
        sudo adduser git
### 初始化git仓库，存放目录 /var/repo/
        sudo mkdir /var/repo
        cd /var/repo
        sudo git init --bare blog.git
### 创建git hooks
创建自定义钩子,指定特定的重要动作发生时触发自定义脚本,创建的是服务端钩子 post-receive，具体内容可以[查看这里](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)

        cd /var/repo/blog.git/hooks
        vim post-receive
在post-receive加入如下内容
    
        #!/bin/sh
        git --work-tree=/var/www/hexo --git-dir=/var/repo/blog.git checkout -f
* 注意，其中/var/www/hexo目录是您的nginx的网站目录，请根据需要进行修改  
* 修改文件的可执行权限  

        chmod +x post-receive    
### 改变 blog.git 目录的拥有者为 git 用户：
        sudo chown -R git:git blog.git

## vps安装nginx服务
[见这里](http://weizhaowu.me/2017/04/13/ubuntu%E4%B8%8B%E7%BC%96%E8%AF%91%E5%AE%89%E8%A3%85nginx/)
## windows本地配置
修改hexo目录下的_config.yml文件中的deploy

    deploy:
    type: git
    repo: git@weizhaowu.me:/var/repo/blog.git
    branch: master
## 具体的使用
    hexo new "new-post"
    hexo clean && hexo generate --deploy
这样博客就会自动更新到网站上面了。
## 参考
[使用 Git Hook 自动部署 Hexo 到个人 VPS](http://www.swiftyper.com/2016/04/17/deploy-hexo-with-git-hook/)




