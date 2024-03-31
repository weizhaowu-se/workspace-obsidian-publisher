---
share: "true"
title: 使用github page以及hexo搭建博客（自定义域名）
date: 2019-10-13 16:15:20
tags: 
---

## 本地安装hexo

### 安装hexo

* 查看文档 [hexo安装](https://hexo.io/zh-cn/docs/index.html#安装)

### 初始化项目

常用命令

```
hexo init <folder>
cd <folder>
npm install
hexo new post '博客标题'   ---新建博文
hexo generate  -d
hexo server   ---本地启动服务器,启动后访问http://localhost:4000/
```

常用文件

```
_config.yml    ---配置文件
source/_posts    ---博客源文件(md文件)
```

## github新建仓库-github page

* 新建仓库,仓库名称为  `{yourusername}.github.io`

## hexo与github page配置关联

* 配置hexo配置文件 `_config.yml `

```
deploy: 
  type: git
  repo: https://github.com/{username}/{username}.github.io.git  ##填写仓库地址
  branch: master
```

* 执行命令

```
hexo g -d
```

* 访问网址

```
https://{yourusername}.github.io
```

## 配置自定义域名

* source目录下新增文件,文件名为  `CNAME` (无后缀),文本内容为自定义域名(无http和www等前缀)
* 配置dns中的c类解析,将自定义域名映射为  ` {yourusername}.github.io`

```
1. 先添加一个CNAME，主机记录写@，后面记录值写上你的http://xxxx.github.io
2. 再添加一个CNAME，主机记录写www，后面记录值也是http://xxxx.github.io
```

## 参考

[hexo安装](https://hexo.io/zh-cn/docs/index.html#安装)

[GitHub Pages 使用入门](https://zhuanlan.zhihu.com/p/25680246)


