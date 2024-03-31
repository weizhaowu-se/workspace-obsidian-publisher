---
share: "true"
title: mac下docker修改国内镜像源
date: 2020-06-17 23:31:33
tags:
  - docker
---

# mac下docker修改国内镜像源

1. 打开Preferences配置
2. 打开配置项

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gfvpiscjs7j31fl0u00x1.jpg)

3. 新增如下配置

```
 "registry-mirrors": [
    "https://md4nbj2f.mirror.aliyuncs.com"
  ]
```


