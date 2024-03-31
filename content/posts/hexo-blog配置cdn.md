---
share: "true"
title: hexo-blog配置cdn
date: 2020-05-13 00:00:49
tags: 
---

# hexo-blog配置cdn

### 前置条件

* hexo，next主题
* 通过GitHub page进行部署

### 问题

* 访问速度慢

<!--more-->

### 解决方案

#### 使用gitee替换github

1. 注册gitee账号，地址 [gitee](https://gitee.com/)
2. 新建仓库
3. gitee中仓库里开启GitHub page服务

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ger6xsrwe6j31q40bqtdm.jpg)

4. 修改hexo的deploy地址

修改hexo项目配置文件`_config.yml`文件中deploy部分

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ger70yvaj8j316804q74y.jpg)

**注意**

* 开启Gitee Pages时，如果项目名称和用户名称不一致的话，那么你的访问地址会有一个后缀，这个后缀是你在创建仓库时所设置时的路径地址（此处我的配置是`hexo`），这种情况下还需要修改hexo项目配置文件`_config.yml`中的root部分配置，否则部署之后会由于路径问题加载不到css和js文件，样式会出现问题
* 使用Gitee Pages时，访问速度的确提升了不少，但是最终还是没有采用这种方式，主要原因是我需要用自己的域名，而Gitee Pages除了自定义域名收费之外，域名还需要是已经备案的，此外，Gitee Pages进行发布之后，还需要每次手动点击下更新，属实麻烦，这几点就导致了我放弃了这种方式了，只能采用第二种方式，尽可能的优化访问速度了。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ger75rwmhgj319q0tojzw.jpg)

#### 使用cdn加速

主要分为两个部分来看

##### 加速常见的通用前端文件

主要是一些比较常见的前端文件，像是jquery这种，next主题里面提供了十分便捷的修改方式，打开主题配置文件`_config.yml`，搜索关键词`Script Vendors`，接下来就很简单了，按照文件名称和版本号一个个去找对应的cdn地址，找不到对应的cdn地址的话就置空就行了，我这边使用使用的几个cdn是

* https://www.bootcdn.cn/
* https://www.jsdelivr.com/

修改后的内容为（供参考）

```
# Script Vendors.
# Set a CDN address for the vendor you want to customize.
# For example
#    jquery: https://ajax.googleapis.com/ajax/libs/jquery/2.2.0/jquery.min.js
# Be aware that you should use the same version as internal ones to avoid potential problems.
# Please use the https protocol of CDN files when you enable https on your site.
vendors:
  # Internal path prefix. Please do not edit it.
  _internal: lib

  # Internal version: 2.1.3
  jquery: //cdn.jsdelivr.net/jquery/2.1.3/jquery.min.js

  # Internal version: 2.1.5
  # See: http://fancyapps.com/fancybox/
  fancybox:  //cdn.jsdelivr.net/fancybox/2.1.5/jquery.fancybox.pack.js
  fancybox_css:  //cdn.jsdelivr.net/fancybox/2.1.5/jquery.fancybox.min.css

  # Internal version: 1.0.6
  # See: https://github.com/ftlabs/fastclick
  fastclick:  //cdn.jsdelivr.net/fastclick/1.0.6/fastclick.min.js

  # Internal version: 1.9.7
  # See: https://github.com/tuupola/jquery_lazyload
  lazyload:  //cdn.jsdelivr.net/jquery.lazyload/1.9.3/jquery.lazyload.min.js

  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity:  //cdn.jsdelivr.net/velocity/1.2.3/velocity.min.js

  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity_ui: //cdn.jsdelivr.net/velocity/1.2.3/velocity.ui.min.js

  # Internal version: 0.7.9
  # See: https://faisalman.github.io/ua-parser-js/
  ua_parser: //cdn.jsdelivr.net/ua-parser.js/0.7.10/ua-parser.min.js

  # Internal version: 4.6.2
  # See: http://fontawesome.io/
  fontawesome: //maxcdn.bootstrapcdn.com/font-awesome/4.6.2/css/font-awesome.min.css

  # Internal version: 1
  # https://www.algolia.com
  algolia_instant_js:
  algolia_instant_css:

  # Internal version: 1.0.2
  # See: https://github.com/HubSpot/pace
  # Or use direct links below:
  # pace: //cdn.bootcss.com/pace/1.0.2/pace.min.js
  # pace_css: //cdn.bootcss.com/pace/1.0.2/themes/blue/pace-theme-flash.min.css
  pace: //cdn.bootcdn.net/ajax/libs/pace/1.0.2/pace.js
  pace_css:

  # Internal version: 1.0.0
  # https://github.com/hustcc/canvas-nest.js
  canvas_nest:

  # three
  three: //cdn.bootcdn.net/ajax/libs/three.js/10/three.js

  # three_waves
  # https://github.com/jjandxa/three_waves
  three_waves:

  # three_waves
  # https://github.com/jjandxa/canvas_lines
  canvas_lines:

  # three_waves
  # https://github.com/jjandxa/canvas_sphere
  canvas_sphere:

  # Internal version: 1.0.0
  # https://github.com/zproo/canvas-ribbon
  canvas_ribbon:

  # Internal version: 3.3.0
  # https://github.com/ethantw/Han
  han:

  # needMoreShare2
  # https://github.com/revir/need-more-share2
  needMoreShare2:
```

##### 加速项目自己的前端文件

除了上面常用的一些前端资源的cdn之外，我们还可以通过jsdelivr来对我们项目自己的前端文件进行加速。

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ger7kkwkajj31p60oy7d7.jpg)

从此处可以看到，jsdelivr将GitHub上的文件都cdn起来了（当然前提是你的仓库是公开的），那么我们就可以使用这个cdn来加载我们项目里的前端文件了，同样是修改next主题配置文件`_config,yml`，搜索`Assets`，下面是修改前后的对比（注释掉的内容是修改前）

```
# Assets
# css: css
# js: js
# images: images
css: //cdn.jsdelivr.net/gh/user/repo@master/css
js: //cdn.jsdelivr.net/gh/user/repo@master/js
images: //cdn.jsdelivr.net/gh/user/repo@master/images
```

* 注意将user和repo修改为自己对应的用户名和仓库名称
* 可以先自己尝试着看能不能访问到对应的js文件来进行验证

##### 验证

* 打开网站，查看js等文件的加载地址，可以看到已经是通过cdn进行访问了

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ger7pt5i60j310o09ctb4.jpg)

* 查看页面，访问起来速度的确加快了不少，但是由于index.html依然是需要从GitHub服务器加载，所以访问速度也还是不甚理想。

### 参考

* [Next主题 设置 「JavaScript 第三方库」](https://theme-next.iissnan.com/advanced-settings.html)

* [使用 jsDelivr 免费加速 GitHub Pages 博客的静态资源](https://mazhuang.org/2020/05/01/cdn-for-github-pages/)

* [尝试折腾了下用 Hexo-Next-Theme 搭建的博客](https://leay.net/2020/03/23/hexo-next/)
* [知乎-如何为备不了案的域名CDN加速？](https://www.zhihu.com/question/364028809)

* [使用Gitee+Hexo搭建个人博客](https://www.jianshu.com/p/5014133ba61a)
* [当要部署的项目与自己的个性地址不一致时，部署完成后存在一些资源访问404](https://gitee.com/help/articles/4136#article-header0)
