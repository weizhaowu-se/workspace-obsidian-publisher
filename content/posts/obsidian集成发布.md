---
title: obsidian集成发布
share: "true"
date: 2024-03-31 17:56:04
---


使用主题
https://github.com/adityatelange/hugo-PaperMod/wiki/Installation

参考文档：
https://www.printlove.cn/obsidian-blog/

https://www.printlove.cn/github-publisher-hugo/

主要步骤：
1. 本地初始化hugo，上传到github
2. obsidian安装插件，将源文件上传到github目录（注意附件的处理）
3. 使用# Vercel  自动部署hugo（配置cname域名）

插曲：由于将历史的文档一次性上传，加之每次commit都会触发一个自动部署，导致自动部署失败

https://oragekk.me/tutorial/CI_CD/vercel-deploy.html#_7-%E7%B3%BB%E7%BB%9F%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E4%B8%80%E8%A7%88%E8%A1%A8

结合template 和quickadd快速新增文章
https://lillianwho.com/posts/obsidian-hugo-cloudflare/

批量编辑属性：
https://forum-zh.obsidian.md/t/topic/5948


待办事项：主题配置更新 https://www.shaohanyun.top/posts/env/blog_build2/  需要考虑toml配置文件和yaml配置文件

