---
title: 通过cloudflare将内网服务映射到公网
share: "true"
date: 2024-09-23 22:21:42
---
前提条件：
1. 已经托管到cloudflare的域名
2. （黑）群晖上部署cloudflare/cloudflared（docker方式部署）
3. 针对每个服务在cloudflare上配置对应的域名和转发
# 将域名托管到cloudflare
在域名注册商（此处为腾讯云）中，将DNS解析服务器设置为cloudflare对应的解析服务器【原先是右边的，需要修改为左边内容】，修改完成即可将域名添加到cloudflare中，cloudflare会自动将原先的DNS解析配置到cloudflare中

![file-20240924224437.png](file-20240924224437.png)
# 群晖上部署cloudflare
此处参照文章  [黑群晖服务使用 Cloudflare tunnel 进行内网穿透教程]( https://hackfang.me/nas-cloudflare-tunnel  )
1. 新建隧道获取token
2. 使用第一步获取的token，在w 群晖的容器服务中安装 cloudflare/cloudflared【特别注意网络需要设置为host类型以及启动命令为tunnel --no-autoupdate run --token <复制的token>】
3. 启动完成之后，此时在cloudflare的tunnel中即可看到status变为绿色
# 针对每个服务在cloudflare上配置对应的域名和转发
点击隧道，进入Public Hostname，点击添加按钮
![file-20240924224449.png](file-20240924224449.png)
添加完成之后，即可使用对应的子域名访问内网对应的服务了
* 可以到DNS解析记录可以看到会自动添加一条子域名对应的解析记录，对应的解析值为隧道ID